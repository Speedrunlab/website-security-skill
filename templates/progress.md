# Website security progress: <site>

Private file. It records the server's address and the open weaknesses, so it lives
outside the website's folder and outside any repository, with the backups.

- Started: <date>
- Owner: <name>
- Permission confirmed: <yes, and how> on <date>

## Setup map

- Domain registered with: <company>; login held by: <who>
- DNS answered by (nameservers): <names>
- Hosting: <server or hosting account with its own address / managed platform: which>
- Server's own address (private): <address, or "managed platform: none">
- Domain mail (MX) handled by: <provider or "the web server">; mailboxes on the web server: <yes / no>
- Site's own mail (forms, orders, resets) sent by: <PHP mail() / service>
- Site software: <WordPress version / other>; admin sign-in held by: <who>
- Can the owner run commands on the server: <SSH with WP-CLI / panel only>
- Two-step login on: registrar <yes/no>, DNS provider <yes/no>, host or platform <yes/no>, email service <yes/no>

## Steps

| # | Step | Status | Evidence | Date |
|---|---|---|---|---|
| 1 | Own backups, restore tested | done / skipped / open | newest copy date, where each copy lives, restore result | |
| 2 | Own copy of the DNS records | | export date and where it is kept | |
| 3 | Site email through a proper service | | SPF, DKIM, DMARC results of the test message; server address in headers or not | |
| 4 | Cloudflare, server's address private | | cf-ray seen; leak check result; direct probe exit codes | |
| 5 | Proxy protections in use | | managed rules; Bot Fight Mode decision and why; Under Attack mode known | |
| 6 | Plugins, themes, scripts updated | | updates waiting; unused removed; auto-updates; checksums | |
| 7 | No nulled or cracked plugins | | source of each plugin; custom plugin review date | |
| 8 | Wayback Machine known as last resort | | newest capture date | |

For a skipped or open step, write why, and whose move it is.

## Outside these steps

Things noticed that are not part of the eight steps, mentioned once at the end.

-

## Repeat

- Restore test: <how often, next date>
- DMARC reports: read on <date>; move to quarantine, then reject, when only your services appear
- DNS export: after every DNS change
- Update check: <how often>, and auto-updates for: <list>
- Custom plugin review: <next date>
