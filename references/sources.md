# Sources

Every fact in this skill comes from the organisation responsible for it: the provider's
own documentation, a government cyber security agency, or the standard itself. Each page
was read between 28 and 30 October 2026. Providers change their products and pages; when a step
depends on a detail, check the live page before relying on it.

## Step 1. Backups

- **CISA**, Back Up Business Data: the 3-2-1 rule; encryption and offline copies;
  scheduled recovery tests; being able to roll back at least seven days.
  https://www.cisa.gov/audiences/small-and-medium-businesses/secure-your-business/back-up-business-data
- **ASD's ACSC**, Small business cyber security guide (January 2025): backups as a regular
  or automatic practice; for websites, back up so you can restore after a cyber attack.
  https://www.cyber.gov.au/sites/default/files/2025-03/Small%20business%20cybersecurity%20guide%20%28January%202025%29.pdf
- **WordPress**, Hardening WordPress: back up regularly, including the database.
  https://developer.wordpress.org/advanced-administration/security/hardening/
- **WP-CLI**, wp db export: exports the database with mysqldump, using the settings in
  wp-config.php. https://developer.wordpress.org/cli/commands/db/export/

## Step 2. DNS records

- **Cloudflare**, Import and export records: BIND zone export, the `cf-proxied` tags.
  https://developers.cloudflare.com/dns/manage-dns-records/how-to/import-and-export/
- **Cloudflare**, Review audit logs: all plans, kept 18 months.
  https://developers.cloudflare.com/fundamentals/account/account-security/review-audit-logs/
- **Cloudflare**, Protect your origin server: historical records keep addresses from
  before joining Cloudflare.
  https://developers.cloudflare.com/fundamentals/security/protect-your-origin-server/

## Step 3. Email

- **Google**, Email sender guidelines: requirements for all senders and for senders of
  more than 5,000 messages a day, from 1 February 2024.
  https://support.google.com/a/answer/81126
- **Google**, Trace an email with its full headers (Show original).
  https://support.google.com/mail/answer/29436
- **IETF**, RFC 5321 (SMTP), section 4.4: receiving servers insert a Received line, which
  should include the sending machine's IP address. https://www.rfc-editor.org/rfc/rfc5321.html
- **IETF**, RFC 7208 (SPF): one SPF record per domain, more is a permerror (sections 3.2
  and 4.5); at most 10 terms that need DNS lookups, more is a permerror (4.6.4); the
  `a` and `mx` terms match the domain's own addresses and its mail hosts (5.3, 5.4).
  https://www.rfc-editor.org/rfc/rfc7208.html
- **IETF**, RFC 7489 (DMARC): the `p` and `rua` tags (section 6.3); aggregate reports
  are XML files sent in an email message (section 7.2.1.1).
  https://www.rfc-editor.org/rfc/rfc7489.html
- **IETF**, RFC 8601: the Authentication-Results header. https://www.rfc-editor.org/rfc/rfc8601.html
- **PHP**, mail(): uses the program set by `sendmail_path`.
  https://www.php.net/manual/en/function.mail.php
- **Cloudflare**, Protect your origin server: do not host mail on the server you protect.
  https://developers.cloudflare.com/fundamentals/security/protect-your-origin-server/

## Step 4. Cloudflare and the server's address

- **Cloudflare**, Full setup: a registered domain; review imported records; DNSSEC off
  before changing nameservers; up to 24 hours; Cloudflare Registrar.
  https://developers.cloudflare.com/dns/zone-setups/full-setup/setup/
- **Cloudflare**, Proxy status: proxied records answer with Cloudflare addresses; only A,
  AAAA and CNAME can be proxied. https://developers.cloudflare.com/dns/proxy-status/
- **Cloudflare**, Encryption modes, and Full (strict): what each mode encrypts; Origin CA
  and public certificates accepted. https://developers.cloudflare.com/ssl/origin-configuration/ssl-modes/
  and https://developers.cloudflare.com/ssl/origin-configuration/ssl-modes/full-strict/
- **Cloudflare**, DNSSEC: enabling it on Cloudflare, the DS record at the registrar.
  https://developers.cloudflare.com/dns/dnssec/
- **Cloudflare**, Nameservers: the `<name>.ns.cloudflare.com` pattern.
  https://developers.cloudflare.com/dns/nameservers/
- **Cloudflare**, Protect your origin server: audit DNS-only records, mail, rotate origin
  IPs, allow lists, Tunnel, Authenticated Origin Pulls, and their security ratings.
  https://developers.cloudflare.com/fundamentals/security/protect-your-origin-server/
- **Cloudflare**, Cloudflare IP addresses: the published ranges change rarely.
  https://developers.cloudflare.com/fundamentals/concepts/cloudflare-ip-addresses/ (list:
  https://www.cloudflare.com/ips/)
- **Cloudflare**, Cloudflare HTTP headers: the Cf-Ray header returned to visitors, and
  CF-Connecting-IP, which carries the visitor's address to the server.
  https://developers.cloudflare.com/fundamentals/reference/http-headers/
- **Vercel**, Using Cloudflare and other reverse proxies with Vercel: connect directly
  where possible; Bot Protection does not work behind a reverse proxy; Cloudflare as DNS
  only. https://vercel.com/kb/guide/can-i-use-a-proxy-on-top-of-my-vercel-deployment
- **curl**, the manual: `--resolve`, and the exit codes 7, 28, 35 and 60.
  https://curl.se/docs/manpage.html

## Step 5. The proxy's protections

- **Cloudflare**, DDoS protection: available on all plans, unmetered (layers 3 to 7).
  https://developers.cloudflare.com/ddos-protection/
- **Cloudflare**, WAF managed rules: the Free Managed Ruleset.
  https://developers.cloudflare.com/waf/managed-rules/
- **Cloudflare**, Bot Fight Mode: what it does and its limits.
  https://developers.cloudflare.com/bots/get-started/bot-fight-mode/
- **Cloudflare**, Under Attack mode: one of the last resorts during an attack.
  https://developers.cloudflare.com/fundamentals/reference/under-attack-mode/

## Step 6. Updates

- **ASD's ACSC**, Small business cyber security guide (January 2025): keeping software
  up to date; regularly updating a website's CMS and plugins (link above).
- **WordPress**, Plugin and themes auto-updates: from WordPress 5.5, twice a day, email
  notices. https://wordpress.org/documentation/article/plugins-themes-auto-updates/
- **WP-CLI**: core check-update, plugin list, theme list, core verify-checksums and
  plugin verify-checksums.
  https://developer.wordpress.org/cli/commands/core/check-update/,
  https://developer.wordpress.org/cli/commands/plugin/list/,
  https://developer.wordpress.org/cli/commands/theme/list/,
  https://developer.wordpress.org/cli/commands/core/verify-checksums/ and
  https://developer.wordpress.org/cli/commands/plugin/verify-checksums/
- **WP-CLI source**: the values of `wporg_status`, and the checksum messages.
  https://raw.githubusercontent.com/wp-cli/extension-command/main/src/Plugin_Command.php and
  https://raw.githubusercontent.com/wp-cli/checksum-command/main/src/Checksum_Plugin_Command.php
- **npm**, npm audit. https://docs.npmjs.com/cli/v10/commands/npm-audit
- **Composer**, the audit command. https://getcomposer.org/doc/03-cli.md

## Step 7. Plugins from trusted sources, and building your own

- **WordPress**, Hardening WordPress: trusted sources; delete unused plugins (link above).
- **WordPress**, Common APIs Handbook, Security: validate and sanitise input, escape late.
  https://developer.wordpress.org/apis/security/
- **WordPress**, Nonces: help against CSRF; never relied on for access control; check
  `current_user_can()`. https://developer.wordpress.org/apis/security/nonces/
- **WordPress**, wpdb::prepare: prepares a SQL query for safe execution.
  https://developer.wordpress.org/reference/classes/wpdb/prepare/

## Step 8. The Wayback Machine

- **Internet Archive**, Using the Wayback Machine: browsing captures, what is not
  archived, Save Page Now. https://help.archive.org/help/using-the-wayback-machine/
- **Internet Archive**, See whether a website exists in the archives: the availability
  lookup. https://archive.org/developers/tutorial-get-snapshot-wayback.html

## Where the eight steps came from

The eight steps come from a post on r/Entrepreneurs by u/HardenedWeb-Official. The post
is the inspiration, not a source: every fact above was checked at the organisation
responsible for it, and the skill is written in our own words. Not affiliated.
