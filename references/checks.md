# Checks

Two kinds of check, and one section that changes files:

- **Lookups** (Setup map, Record snapshot, Proxy check, Leak check, Direct probe, Email
  records, DNSSEC, Wayback lookup) only read, and run from anywhere: a shell on macOS
  or Linux with `dig` and `curl`, or WSL on Windows. These are yours to run.
- **Server and project checks** (Backup files in the web root, WordPress, Code
  projects) only read, but run on the web server or in the project. The owner runs
  them and pastes the output, unless they have given you that access and said yes.
- **Making a backup** writes files on the server. The owner runs it, or says yes first.

Replace `example.com` with the owner's domain, and `203.0.113.10` with the server's own
address from the hosting panel. Both example values are reserved for documentation.

## Setup map

```sh
D=example.com
dig +short NS "$D"        # who answers DNS for the domain
dig +short A "$D"         # the address the domain points at
dig +short A "www.$D"
dig +short AAAA "$D"      # IPv6, if any
dig +short MX "$D"        # who handles the domain's mail
```

How to read it:
- Nameservers ending in `ns.cloudflare.com` mean Cloudflare runs the DNS.
- If the A or AAAA answer is the server's own address, DNS shows that address to
  everyone today.
- If an MX host resolves to the server's own address (`dig +short A <mx host>`), the
  domain's mailboxes live on the web server.

## Record snapshot

Compare this with the owner's zone export (step 2). `dig` only answers for the names it
is asked about, so the export remains the full list.

```sh
D=example.com
for t in A AAAA CNAME MX TXT NS CAA; do printf '%-6s' "$t"; dig +short "$t" "$D" | tr '\n' ' '; echo; done
dig +short A "www.$D"
dig +short A "mail.$D"
dig +short TXT "_dmarc.$D"
```

## Proxy check

```sh
curl -sI --max-time 15 "https://www.example.com/" | grep -i '^cf-ray:'
```

A `cf-ray:` line means the response came through Cloudflare. No line means it did not,
or the request failed: run it again without `| grep` to see which. Check every name the
site uses (the bare domain, `www`, and any API or callback address the owner names).

## Leak check

Live lookups:

```sh
D=example.com
dig +short MX "$D"                                 # then, for each mail host it names:
dig +short A mail.example.com                      # the server's own address? mail lives on it
dig +short TXT "$D" | grep -oE 'ip[46]:[^ "]+'     # addresses written into the SPF record
```

From a Cloudflare zone export (DNS Records page, Import and Export, Export), where every
record carries a `cf-proxied:true` or `cf-proxied:false` tag:

```sh
grep -E '[[:space:]]IN[[:space:]]+(A|AAAA)[[:space:]]' zone-export.txt | grep -v 'cf-proxied:true'
grep -E '[[:space:]]IN[[:space:]]+TXT[[:space:]]' zone-export.txt | grep -E 'ip[46]:'
```

Every line from the first command is an address record that is DNS only; any that holds
the server's address shows it to anyone who asks. Every line from the second is a TXT
record that names addresses; check whether the server's is among them.

SPF can also name the server without writing its address: an `a` term matches the
domain's own addresses and an `mx` term its mail hosts' (RFC 7208). Read the whole SPF
record, not only the `ip4:` and `ip6:` parts.

## Direct probe

Asks the server's own address for the site, skipping DNS and Cloudflare. Run it from a
network that is not on the server's allow list (a phone hotspot, for example), because
the owner's own address may be allowed through.

```sh
H=www.example.com      # the site's hostname
IP=203.0.113.10        # the server's own address
curl -sI --max-time 10 --resolve "$H:443:$IP" "https://$H/" -o /dev/null -w '%{http_code}\n'
echo "curl exit: $?"
curl -sI --max-time 10 --resolve "$H:80:$IP" "http://$H/" -o /dev/null -w '%{http_code}\n'
echo "curl exit: $?"
```

For an IPv6 address, put it in brackets: `--resolve "$H:443:[2001:db8::10]"`.

| Result | What it means |
|---|---|
| Exit 0 with a status code (200, 301, 403...) | The server answered directly. It is not locked to Cloudflare yet. |
| Exit 35, 60 or another TLS error, status `000` | Something at that address answered, but its certificate did not check out for this name. It still answers directly. |
| Exit 7, status `000` | Failed to connect: the connection was refused. Nothing answers there from this network. |
| Exit 28, status `000` | Timed out: no answer. Nothing answers there from this network. |

Exit 7 or 28 on both ports is the result step 4 is after. The probe covers the web
ports only. If the server also runs mail or a hosting panel, those ports answer by
design while the services stay on it; record them in the progress file.

## Email records

```sh
D=example.com
dig +short TXT "$D" | grep -i 'v=spf1'        # exactly one line
dig +short TXT "_dmarc.$D"                     # starts v=DMARC1; p=none, quarantine or reject
dig +short TXT "SELECTOR._domainkey.$D"        # SELECTOR comes from the email service
dig +short CNAME "SELECTOR._domainkey.$D"      # some services publish DKIM as a CNAME
```

How to read it:
- SPF: one `v=spf1` line. Two or more is an error in itself (RFC 7208 gives a
  `permerror`): merge them into one.
- DMARC: a record starting `v=DMARC1;` with `p=none` while reports are watched, and an
  `rua=mailto:` address that someone reads.
- DKIM: any answer to either lookup means the selector is published; no answer to both
  means it is not.

The test message: send one from the site's form to a Gmail address, open it, and choose
More, then Show original. Ask the owner to paste only the Authentication-Results line
and the Received lines, because the rest of the message can hold customer data. The
Authentication-Results line should read `spf=pass`, `dkim=pass` and `dmarc=pass`. Then
search the Received lines for the server's own address.

## DNSSEC

```sh
dig +short DS example.com
```

An answer means DNSSEC is on for the domain at the registrar: turn it off there before
changing nameservers (step 4). No answer means it is off.

## Backup files in the web root

Run on the server. Lists files with backup-style names inside the folder the website is
served from, where anyone who guesses a name can download them.

```sh
find /path/to/webroot -type f \( -name '*.zip' -o -name '*.tar' -o -name '*.gz' -o -name '*.tgz' -o -name '*.7z' -o -name '*.rar' -o -name '*.sql' -o -name '*.bak' -o -name '*.old' -o -name '*.wpress' \)
```

Each file listed is moved out of the web root to where the backups live, after the owner
confirms there is a copy elsewhere. Ordinary plugin downloads can show up too; the
owner says which are backups. The name list is not complete, so also ask about any
large file in the web root the owner does not recognise.

## Making a backup

This section writes files on the server: the owner runs it, or says yes first. Run in
the site's folder. `wp db export` reads the database settings from `wp-config.php` and
would write into the current folder, the web root, without a path, so it gets one
outside it. `umask 077` keeps the new files readable by the owner's account only,
because they hold customer data and the site's own passwords.

```sh
umask 077
mkdir -p "$HOME/backups"
wp db export "$HOME/backups/db-$(date +%F).sql"
tar -czf "$HOME/backups/files-$(date +%F).tar.gz" -C /path/to/webroot .
```

Then the owner downloads the `backups` folder to a computer or storage account that is
not the host (SFTP, or the hosting panel's file manager), keeps that copy encrypted, and
confirms the dump and the archive open.

## WordPress

Run on the server, in the site's folder.

```sh
wp core check-update                       # newer WordPress versions, if any
wp plugin list --update=available          # plugins with an update waiting
wp theme list --update=available           # themes with an update waiting
wp plugin list --status=inactive           # installed but unused: candidates to delete
wp theme list --status=inactive
wp plugin list --fields=name,version,author,wporg_status,wporg_last_updated
wp core verify-checksums                   # core files against WordPress.org's checksums
wp plugin verify-checksums --all           # plugin files against WordPress.org's checksums
```

How to read it:
- `wporg_status` is `active` for a plugin listed on WordPress.org, `closed` for one that
  was listed and has been closed (find out why on its WordPress.org page before keeping
  it), and empty for one WordPress.org has never listed: ask the owner where that one
  came from.
- `verify-checksums` reports `File was added` or `Checksum does not match` for a file
  that differs from the published version. Stop and look at that file before anything
  else. For a plugin WordPress.org does not have, it warns `Could not retrieve the
  checksums ... skipping`: that plugin was not checked.

Without WP-CLI, in the WordPress admin: Dashboard, Updates shows what is waiting, and
the Plugins screen lists every plugin with its version and author. The checksum checks
have no admin equivalent: record them as not checked.

## Code projects

Run in the project's folder. Both only read.

```sh
npm audit          # Node: known vulnerabilities in the project's dependencies
composer audit     # PHP: security advisories, abandoned packages and packages flagged as malware
```

`npm audit fix` changes the project's files, so it waits for the owner's yes, then a
test of the site before it goes live.

## Wayback lookup

```sh
curl -s "https://archive.org/wayback/available?url=example.com"
```

A `closest` entry gives the newest capture's address and its `timestamp`
(YYYYMMDDhhmmss). To browse everything archived for the site, open
`http://web.archive.org/*/www.example.com/*` in a browser. The service limits how often
it can be asked; if it answers "429 Too Many Requests", wait and try once more.
