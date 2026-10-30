---
name: website-security-skill
description: "Website Security Skill: walk the owner of a website through eight security set-up steps, one at a time, in plain English: their own tested backups, their own copy of the DNS records, the site's email sent through a proper email service with SPF, DKIM and DMARC, the domain on Cloudflare with the server's address kept private, the protections in front of the site, plugins, themes and scripts kept updated, no nulled or cracked plugins, and the Wayback Machine as the last resort. Use when someone wants to secure a website they own or run: a new domain going live, a WordPress or other site on shared hosting or its own server, a move to Cloudflare, contact-form email landing in spam, a tempting nulled plugin, or a site lost with its backups."
license: MIT
compatibility: Any agent that can hold a conversation. A shell with dig and curl for the read-only lookups is optional. Anything that must run on the web server, the owner runs and pastes back. Needs no credentials, because the owner makes every account change in their own dashboards.
metadata:
  version: 1.0.0
  facts-checked: 2026-10-30
  author: Speedrun AI Labs
  tags: website-security, cloudflare, dns, backups, email-authentication, wordpress, small-business
---

# Website Security Skill

Most small-business websites are set up once and left alone. The server's address
is public, the only backup is the host's, the contact form sends mail that receivers
cannot verify, and a cheap "nulled" plugin is one click away. This skill walks the
owner through eight steps that close those gaps, one step at a time, checking each
one before moving on.

You guide, the owner decides. The owner makes every change in their own accounts.
You explain what to do and why, run read-only lookups, and confirm that each step
worked.

## Ground rules

1. **Only for a site the person owns, or runs with the owner's permission.** If their
   message does not already say so, ask in your first message, in the same sentence
   as asking for the domain. If the answer is no, stop and say why.
2. **No secrets, ever.** Never ask for a password, API token, recovery code or private
   key, never use one that is pasted into the chat, and never open files that hold
   them (`.env`, `wp-config.php`, key files): ask for a setting's name, not its value.
   If a secret is pasted anyway, tell the owner to replace it, because the chat now
   holds it.
3. **Who does what.** From your side you run read-only lookups: `dig` and `curl`
   (`references/checks.md`). Anything on the web server (WP-CLI, `find`, making a
   backup) the owner runs and pastes back, or approves you running if they have given
   you that access. Anything that changes DNS, nameservers, a firewall, email, plugins
   or files waits for the owner's clear yes, with the way back written down first.
4. **One message, one job.** Each message carries one step, or one part of a step:
   what it does in a sentence, why in a sentence or two with the source named, at most
   three actions, then "tell me when you're done". A step can take several turns:
   split it rather than crowd it. One question per message.
5. **Backups before changes.** Steps 1 and 2 come before any nameserver, firewall or
   email change, because they are the way back. If the owner wants to go ahead without
   them, say what that risks and record their decision.
6. **Facts.** The facts in this skill were checked at the sources in
   `references/sources.md` between 28 and 30 October 2026; repeat them with the source
   named. If the owner's decision rests on one and you can browse, open the page and
   use what it says today.
   Anything beyond them (another provider's behaviour, a price, a statistic, a dated
   security advisory) you check at the provider's own page in this conversation, or
   say "I haven't checked that". Describe the kind of service the owner needs; if they
   ask for names, say you have no affiliation, and compare what the providers' own
   current pages say (sending limits, DKIM support, price).
7. **Say what was verified, never "you're safe".** Each step ends with what was
   checked, how, and what is still open. A skipped or open step is written down with
   its reason.
8. **Safety first, then the order.** If the owner's message shows something that may
   already be harming the site (a nulled plugin installed, signs of a break-in), say so
   in your first reply and start with step 7's "Already installed" part. If they want
   only some of the steps, do those (after steps 1 and 2 when they change anything)
   and list the rest as not done.
9. **Stay on these eight steps.** Something outside them that you notice (a domain
   about to expire, an old PHP version, the server's operating system updates) goes
   in the progress file's "Outside these steps" list and is mentioned once at the end.
   Two-step login on the accounts the steps use is part of the steps (setup map, item 6).
10. **Keep a progress file** from `templates/progress.md`, outside the website's folder
    and outside any repository, because it holds the server's address and the open
    weaknesses. If you cannot write files, keep the same headings in the chat.

If you cannot run commands, give the owner the command (or the dashboard page) and ask
them to paste the output. For an email header, ask for only the Authentication-Results
and Received lines: the rest can hold customer data. A check nobody ran is recorded as
"not checked".

## First: map the setup

Your first message may give the order of the steps as a short list, and asks one thing:
the domain, and the permission if it is not already clear (an owner who says "my site"
has said it). Then run the "Setup map" lookups in `references/checks.md` and ask only
what they cannot show, one question per message, writing each answer into the progress
file.

1. **The domain:** which company is it registered with, and who holds that login?
2. **DNS:** who answers for the domain now? (The NS lookup shows it. Cloudflare's
   nameservers end in `ns.cloudflare.com`.)
3. **Hosting:** where does the website run?
   - **Shared hosting** (a hosting account on the host's servers, often with cPanel or
     Plesk, or a managed WordPress host): the host runs the server and its firewall,
     and can tell the owner its address.
   - **The owner's own server** (a virtual server they run, with or without cPanel or
     Plesk on it): the owner runs its firewall and its updates. A panel on a server the
     owner runs puts it in this group, not the one above.
   - **A managed platform that runs the edge** (Vercel, Netlify, Shopify, Squarespace,
     Wix and similar): there is no server address of the owner's to hide. Step 4 takes
     its platform path and step 5 becomes the platform's own security settings. The
     other steps still apply, adapted: in a code project the public folder is the web
     root, the dependencies are its plugins, and the platform's environment settings
     are the site's settings.
4. **Email:** who handles mail for the domain (the MX records), do the mailboxes live
   on the web server, and what sends the site's own mail (forms, order emails,
   password resets)?
5. **The site:** WordPress, another CMS or custom code; who can sign in to its admin;
   and can the owner run commands on the server (SSH with WP-CLI), or only use a panel?
6. **Two-step login:** is it on for the accounts the steps will use: the registrar,
   the DNS provider, the host or platform, and the email service? Whoever holds those
   logins controls the site. Turning it on comes before the step that uses that account.

## The eight steps, in order

Backups and the DNS copy come first because they are the way back. Email moves before
the Cloudflare step because mail sent from the web server carries its address, so a new
address would leak again straight away.

### Step 1. Back up the site yourself, and test a restore

**Why:** a host's backups live with the host; if the account, the backup software or
the host fails, they fail with it. CISA's 3-2-1 rule: three copies of important files,
on two different types of storage, one of them off-site. CISA also advises scheduled
recovery tests and being able to roll back at least seven days.

**The owner:**
1. Lists what a full copy is: the site's files (for WordPress the whole install,
   uploads included), the database, the mailboxes if they live on the web server, and
   settings the host keeps outside them. Treats every copy as secret: it holds customer
   data and the site's own passwords.
2. Sets an automatic schedule, frequent enough that losing the gap is bearable, and
   keeps at least one copy away from the host (their own computer, an external drive
   kept disconnected, or a storage account separate from the host), encrypted.
3. Restores one backup to a staging or local copy and checks the site works. If that
   is out of reach today: opens the backup, confirms the database export and the files
   are there and readable, and records the full restore test as open, with a date.

**You check:** the date of the newest copy and where each copy lives (the owner tells
you), and that no backup file sits in the site's public folder, where anyone who
guesses its name can download it ("Backup files in the web root"). The commands under
"Making a backup" change files on the server, so the owner runs them or says yes
first, and they copy the files and the database only: mailboxes come from the hosting
panel's own backup or the mail provider's export.

**Done when:** a dated copy of every part of the full copy (mailboxes included when
they live on the server) is off the host, a restore has worked (or the readable check
is done and the full test has a date), and the schedule is on.

### Step 2. Keep your own copy of the DNS records

**Why:** if a record is deleted, or DNS moves to another provider, the exact list is
what puts it back. DNS-history websites sometimes show old records, but they are third
parties and incomplete, and that same history is how attackers find a server's old
address (step 4).

**The owner:**
1. Exports the zone from the DNS provider. On Cloudflare: the DNS Records page, Import
   and Export, Export (a BIND zone file). Elsewhere, the provider's export; if the panel
   has none, asks the provider's support for one, because a hand copy cannot be checked
   for completeness.
2. Keeps the export with the backups, and exports again after every DNS change. Later
   steps always compare against the latest export.

**You check:** the "Record snapshot" against the export. `dig` only sees the names it is
asked about, so the export is the full list.

**Done when:** a dated export is stored with the backups and agrees with what DNS
answers. On Cloudflare, the account's Audit Logs (all plans, kept 18 months) also show
when changes were made.

### Step 3. Send the site's email through a proper email service

**Why:** two reasons.
- **Delivery.** Since 1 February 2024 Gmail requires every sender to set up SPF or
  DKIM, valid forward and reverse DNS, and TLS; senders of more than 5,000 messages a
  day to Gmail also need SPF, DKIM and DMARC, with the From domain aligned. Mail the
  web server sends itself (PHP's `mail()` hands it to the server's own mail program)
  passes these checks only if that server was set up for them. Sending through a
  service set up for the domain turns that into something you can see pass, in the
  test below.
- **The server's address.** Every mail server that receives a message adds a Received
  line at the top, which should include the sending machine's IP address (RFC 5321),
  so mail sent straight from the web server can show its address to every recipient.
  Cloudflare advises not hosting mail on the server you want to protect.

**Look first** when the owner's complaint is mail landing in spam: the Authentication-
Results and Received lines of one message the site sends today show which checks fail
and whether the server's address shows.

**Decide first** when the mailboxes live on the web server: will they move to an email
provider? The answer decides what the SPF and MX records end up naming, so it comes
before any record changes. Moving them is a bigger change and the owner's call;
record it either way. While they stay, staff mail also leaves from the server and the
MX record keeps showing its address.

**The owner:**
1. Chooses a service for the site's mail: a transactional email service, or the SMTP
   service of their business email provider. Verifies the domain with it and adds the
   DNS records it asks for. A domain has one SPF record (RFC 7208), so a new sender is
   merged into the existing record, and the merged record may need at most 10 DNS
   lookups or it fails (also RFC 7208).
2. Adds a DMARC record at `p=none` with a report address (`rua=`), and puts a date to
   read the reports in the progress file; moves to `quarantine`, then `reject`, once
   the reports show only their own services sending. The reports arrive by email at
   that address as XML files meant for software (RFC 7489), so agree first who will
   read them and with what.
3. Connects the site to the service (its SMTP settings or the service's plugin),
   typing the credentials into the site's own settings, never the chat. Sends from an
   address at their own domain; a contact form puts the visitor's address in Reply-To,
   never in From, or the message fails DMARC alignment.

**You check:** "Email records". Then the owner sends a message from the site's form to
a Gmail address, opens Show original and pastes the Authentication-Results and Received
lines: they should show `spf=pass`, `dkim=pass` and `dmarc=pass`. Look for the server's
own address in the Received lines. The service's mail server adds a Received line too
when the site hands it the message, so check rather than assume it is gone. Passing
these checks is what receivers look at first; it does not promise the inbox, so say so.

**Done when:** a test message passes SPF, DKIM and DMARC at Gmail, the date to read the
DMARC reports is in the progress file, and anything that still shows the server's
address is recorded with the owner's decision.

### Step 4. Put the domain on Cloudflare, and keep the server's address private

**Why:** with the proxy on, DNS answers with Cloudflare's addresses instead of the
server's. That only holds if nothing else gives the address away: records left DNS
only, mail from the server (step 3), SPF records listing it, and DNS history from
before the move. Cloudflare documents each of these.

**Platform path** (a managed platform in the setup map): read the platform's own
guidance on putting a proxy in front of it before changing anything. Vercel, for
example, recommends connecting directly, says its Bot Protection managed ruleset does
not work behind a reverse proxy, and supports keeping Cloudflare as the DNS provider
with the records set to DNS only. Done when the domain points where the platform says,
the site loads over HTTPS on every name it uses, and the decision is recorded.

**Already on Cloudflare:** confirm it with the "Proxy check", skip the move, and start
at "Close the leaks".

**Before the move:** steps 1 to 3 are done (or step 3 is recorded as skipped, with the
owner told that mail will keep showing the server's address), and two-step login is on
for the Cloudflare and registrar accounts.

**The move, over several turns.** The owner:
1. Adds the domain to Cloudflare (it must already be registered; a domain bought
   through Cloudflare Registrar uses Cloudflare DNS automatically), then checks every
   imported record against the latest export and adds anything missing, because
   Cloudflare's scan can miss records and a missing MX or TXT record breaks email.
2. Turns the proxy on (orange cloud) for the A, AAAA and CNAME records that serve the
   website; MX and TXT records are always DNS only. Sets SSL/TLS encryption to Full
   (strict), with a valid certificate on the server (a public one, or a Cloudflare
   Origin CA certificate); Flexible leaves the connection from Cloudflare to the
   server unencrypted. Check the certificate first with the "Direct probe": exit 0
   means a valid public certificate, which Full (strict) accepts; a certificate error
   means either a Cloudflare Origin CA certificate (also accepted) or one Full
   (strict) will refuse, so find out which before switching.
3. Writes down the current nameservers. If the "DNSSEC" check shows it is on, turns it
   off at the registrar first. Then replaces the nameservers at the registrar with the
   two Cloudflare assigns, copied exactly; the change can take up to 24 hours. Once the
   domain is active, DNSSEC can be turned on again in Cloudflare's DNS settings, with
   the DS record it generates added at the registrar. The quick way back, if the site
   misbehaves, is in Cloudflare: set the web records to DNS only and visitors reach
   the server directly again. Putting the old nameservers back is the slow way, up to
   a day.

**Close the leaks, one per turn** (each is in Cloudflare's guidance on protecting the
origin):
- **DNS-only records that point at the server** (often `mail.`, `cpanel.`, `ftp.`,
  `webmail.`): proxy them if they only serve web pages, otherwise move the service or
  record the exposure. A DNS-only record shows the address to anyone who asks.
- **The server's address in TXT records**, usually `ip4:` in SPF. SPF can also name it
  indirectly: an `a` term matches the domain's own addresses (RFC 7208), and with the
  proxy on those are Cloudflare's, so mail still sent from the server can start
  failing SPF. Finish step 3 first, then take the server's address out once the
  server no longer sends the domain's mail, staff mail included.
- **Lock the server so only Cloudflare reaches it.** Allow web traffic (ports 80 and
  443) only from Cloudflare's published ranges (cloudflare.com/ips, which change
  rarely), or use Cloudflare Tunnel, which needs no public address at all. Cloudflare
  rates an IP allow list "moderately secure", because it is open to spoofing, and the
  tunnel "very secure". Before restricting anything, make sure the owner's own way in
  (SSH or the hosting panel) still works. On shared hosting the host runs the
  firewall: ask them. Mail ports answer by design while mail runs on the server. A
  hosting panel's login does not need to be open to everyone: where the owner runs the
  firewall, limit the panel's ports to the owner's own address; otherwise record it as
  open.
- **History.** If the site was live before Cloudflare, or its mail has been sent from
  the server, the address is already public: in DNS history, and in the headers of
  mail already delivered, which cannot be recalled. Cloudflare's advice is to rotate
  the origin IPs once onboarded. Get a new address after the leaks above are closed,
  or the new one leaks too. While the mailboxes stay on the server, the MX record
  shows any new address at once, so rotate only after they move. How depends on the
  host or server provider, so check its documentation. If it cannot be done, the lock
  above matters more.

**You check:** the "Proxy check" (a `cf-ray` header), the "Leak check", and the "Direct
probe" from a network that is not on the allow list: a timeout (curl exit 28) or a
refused connection (exit 7) means the server did not answer; any HTTP status, or a
certificate error, means it still answers directly.

**Done when:** the site answers through Cloudflare, the direct probe gets no answer on
the web ports, and no record shows the server's address except those recorded as left
open with the owner's decision (for example the MX while the mailboxes stay on the
server).

### Step 5. Keep the protections on from day one, and use them

**Why:** the protections only apply to traffic that passes through Cloudflare, and a
nameserver change in the middle of an attack can take up to 24 hours to land. On the
Free plan Cloudflare lists unmetered DDoS protection (layers 3 to 7) and the Cloudflare
Free Managed Ruleset, which covers high-impact and widely exploited vulnerabilities.
On a managed platform this step is the platform's own security settings: read its
documentation and record what is on and why.

**The owner, in the Cloudflare dashboard:**
1. Confirms the Free Managed Ruleset is deployed in the WAF's managed rules.
2. Decides on Bot Fight Mode. It challenges requests that match bot patterns, cannot be
   skipped with WAF custom rules, and may challenge API or mobile app traffic. If the
   site has an app, an API, or payment or booking callbacks, tests them once with it
   on, or leaves it off and records why.
3. Learns where I'm Under Attack mode is and what it is for: Cloudflare designed it as
   one of the last resorts while a zone is under attack. Visitors see an interstitial
   page and need JavaScript, so it is switched off when the attack ends.

**Watch for:** behind the proxy the server sees Cloudflare's addresses; each visitor's
own address arrives in the `CF-Connecting-IP` header. Anything that blocks by address
(a security plugin, a firewall or login-attempt blocker on the server, a log-based
ban) has to be set to read it, or it ends up blocking Cloudflare itself.

**You check:** the `cf-ray` header on the main pages and on any API or callback address
the owner names; the owner reads each setting's name and state back to you.

**Done when:** the managed rules are on, Bot Fight Mode is decided and recorded, and the
owner knows when to turn Under Attack mode on and off.

### Step 6. Keep plugins, themes and scripts updated

**Why:** updates fix security flaws. ASD's ACSC calls keeping software up to date one
of the best ways to protect a business from a cyber attack, and its advice for
websites is to regularly update the content management system and its plugins.

**The owner:**
1. Takes a backup (step 1), then applies the waiting updates for WordPress core,
   plugins and themes (Dashboard, Updates), or the project's dependencies.
2. Turns on auto-updates for the plugins and themes they trust to update unattended
   (WordPress 5.5 and later: plugin by plugin on the Plugins screen, theme by theme in
   Appearance). WordPress runs them twice a day and emails the result.
3. Deletes the plugins and themes the site does not use (WordPress's hardening guide
   says to delete a plugin you are not using), and updates premium plugins through the
   vendor's own licence.

**You check:** "WordPress" in the checks file (updates waiting, inactive plugins and
themes, checksums), or `npm audit` / `composer audit` for a code project. Without
WP-CLI: Dashboard, Updates shows what is waiting and the Plugins screen lists every
plugin; the checksum checks have no admin equivalent, so record them as not checked.

**Done when:** nothing is waiting, unused plugins and themes are gone, auto-updates are
on where the owner chose, and checksums verify. A checksum failure means a file differs
from the published version: stop and look at that file before anything else.

### Step 7. No nulled or cracked plugins: build your own instead

**Why:** a nulled plugin is paid software with its licence check removed, passed on by
someone you do not know. You cannot see what else was changed, and it cannot get
updates from the vendor. WordPress's hardening guide: do not get plugins or themes from
untrusted sources; keep to the WordPress.org directory or well-known companies.

**Already installed:** treat the site as possibly compromised. Deleting the plugin does
not undo what it may have changed. Restore a backup from before it was installed,
replace every password and key the site uses, and get professional help. This is the
one place the skill sends the owner beyond its steps.

**The owner:**
1. Installs plugins and themes only from the WordPress.org directory or the vendor's
   own site.
2. If the paid plugin is out of reach: looks for a directory plugin that covers the
   need, asks the vendor about a smaller licence, or, for a small, well-defined need
   only, builds a plugin with a coding agent as its own separate project. Anything
   holding customers' personal data or payments is better served by a maintained
   product. A custom plugin follows WordPress's security guidance: validate input
   (rejecting bad input beats cleaning it), escape output as late as possible, check
   the user's capability with `current_user_can()`, use nonces against cross-site
   request forgery but never as the only access check, and build database queries
   with `$wpdb->prepare()`.
3. Puts the custom plugin's review in the progress file's Repeat list with a date: the
   agent reviews it for vulnerabilities then, and the owner tests each new version on
   a copy of the site before installing it on the live one.

**You check:** where each plugin came from ("WordPress" in the checks file lists them
with their WordPress.org status). `wp plugin verify-checksums --all` can only compare
plugins published on WordPress.org; for each of the others, and without WP-CLI for all
of them, ask where it was bought and whether the licence is in the owner's account with
the vendor.

**Done when:** every plugin and theme has a known, legitimate source, and any custom
plugin lives in its own project with its next review date.

### Step 8. Know the last resort: rebuilding from the Wayback Machine

**Why:** if the site and every backup are gone, the Internet Archive's Wayback Machine
may hold public captures of its pages. It is not a backup: not every page or date is
captured, password-protected pages are not, scripts that need the original server fail,
and it keeps what its crawler saw on public pages, not the site's database or admin.

**The owner:**
1. Looks up what exists ("Wayback lookup" in the checks file) and opens the newest
   capture to see whether it shows the content: pages built by JavaScript may not
   capture well.
2. In a rebuild, takes back the content (words, and images they own) on a fresh,
   updated install, and never copies old scripts, forms or plugins out of a capture,
   because that is the old, unpatched code.
3. Keeps step 1 as the plan. Save Page Now saves one page at a time, not whole sites.

**You check:** run the lookup and report the newest capture's date and what it shows.

**Done when:** the owner knows where to look, and why it does not replace step 1.

## Finish

Report in plain English: each step done, skipped with its reason, or open with what is
left and whose move it is, with the evidence; then the "Outside these steps" list. Hand
over the progress file to keep with the backups, never in the site's folder, with the
dates to repeat: a restore test, the DMARC reports, a DNS export after every change, the
update check, and the custom plugin review.

## Files

- `references/checks.md`: every lookup and server check, how to run it, how to read it.
- `references/sources.md`: the official source behind each fact in this skill.
- `templates/progress.md`: the progress file.
