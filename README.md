# website-security-skill

**Website Security Skill: your agent walks you through securing your own website, one step at a time, in plain English, and checks each step worked before moving on.**

Free and open source · MIT Licence · Built by [Speedrun AI Labs](https://www.speedrunlab.ai), Sydney, Australia

---

## The problem

Most small-business websites are set up once and left alone. The server's address is public, the only backup is the host's, the contact form sends mail that receivers can't verify, and a cheap "nulled" plugin is one click away. The fixes are well known, but they come as long lists, in the wrong order, with no way to tell whether each one actually worked.

Ask a capable agent for help and you get the same list: every step at once, five questions in one message, brand recommendations, and figures with no source. For someone who isn't technical, that is a wall, not a walkthrough.

## What it does

The skill turns your agent into a patient guide. It maps your setup first (where the domain, DNS, hosting and email live), then takes you through eight steps in a safe order. Each step is one message: what it does, why it matters, at most three things for you to do, then a check that it worked.

1. **Back up the site yourself, and test a restore.** Your own copies, one of them away from the host, and a restore that has actually worked.
2. **Keep your own copy of the DNS records.** An export you can put back after a mistake or a move.
3. **Send the site's email through a proper email service.** SPF, DKIM and DMARC set up and proven with a test message, and the message's headers checked for the server's address.
4. **Put the domain on Cloudflare, and keep the server's address private.** The proxy on, encryption at Full (strict), and every leak closed: DNS-only records, SPF entries, old DNS history, and a server that still answers anyone who knows its address.
5. **Keep the proxy on from day one, and use what it gives you.** The free managed rules, a considered decision on Bot Fight Mode, and knowing when Under Attack mode is for.
6. **Keep plugins, themes and scripts updated.** Backups first, auto-updates where you trust them, unused plugins removed, files checked against the published versions.
7. **No nulled or cracked plugins: build your own instead.** Plugins only from trusted sources, and if a paid one is out of reach, a small one built with your agent in its own project, to WordPress's security guidance.
8. **Know the last resort: rebuilding from the Wayback Machine.** Where to look, what it can and can't give back, and why it never replaces step 1.

Backups come first because they are your way back if a later step goes wrong. Email moves before the Cloudflare step, because mail sent from the web server carries its address, and a new address would leak again straight away.

## How it keeps you safe

- **You make every change.** The agent never asks for a password, API token or recovery code. You click in your own dashboards; the agent explains and checks.
- **Read-only checks.** `dig` and `curl` from the agent's side, and the WordPress command line in list and verify modes on the server. Nothing changes DNS, a firewall or a file without your clear yes.
- **Proof, not reassurance.** Each step ends with what was checked and how: a `cf-ray` header, a probe of the server's own address that gets no answer, a test email that passes SPF, DKIM and DMARC. It never tells you "you're safe".
- **Only official sources.** Every fact comes from the organisation responsible: Cloudflare, Google, CISA, the Australian Signals Directorate's ACSC, WordPress, the Internet Archive, Vercel and the IETF standards. The list is in [`references/sources.md`](references/sources.md).
- **Your sites only.** It is for a site you own, or run with the owner's permission. It asks first.

Works for a WordPress or other site on shared hosting or its own server. On a managed platform (Vercel, Netlify, Shopify and similar) there is no server address of yours to hide, so the skill follows the platform's own guidance on proxies and keeps the other steps.

---

## Install

Put the folder in your agent's skills directory.

### Claude Code
```bash
mkdir -p ~/.claude/skills
git clone https://github.com/Speedrunlab/website-security-skill.git ~/.claude/skills/website-security-skill
```
Then run: `/website-security-skill`

### OpenClaw
```bash
mkdir -p ~/.openclaw/skills
git clone https://github.com/Speedrunlab/website-security-skill.git ~/.openclaw/skills/website-security-skill
```

### Any other agent
Copy the folder into its skills directory, or load `SKILL.md` into its instructions and keep `references/` and `templates/` where the agent can read them.

---

## Quick start

In Claude Code, run `/website-security-skill` and tell it which website you want to secure. It asks one question at a time, then starts with your backups.

No shell? The skill still works in a chat-only agent: it gives you each check to run, or the dashboard page to open, and asks you to paste what you see.

---

## What's in the box

```
SKILL.md                 the walkthrough: ground rules, the setup map, the eight steps, the finish
references/
  checks.md              every read-only check, how to run it and how to read the result
  sources.md             the official source behind each fact
templates/
  progress.md            the private progress file: setup map, each step's status and evidence
```

---

## What it is not

Not a penetration test, a malware clean-up or a full security audit. It covers these eight steps well and says so at the end, with a short list of anything it noticed outside them. If a nulled plugin is already installed, it tells you to treat the site as possibly compromised and get professional help.

---

## Credits

Written and maintained by Speedrun AI Labs. The eight steps come from a post on r/Entrepreneurs by u/HardenedWeb-Official, "Entrepreneurs with websites, please read this completely and you'll thank me later". We checked each step against official documentation, put them in a safe order, filled the gaps the documentation showed (locking the server so only Cloudflare reaches it, changing the server's address after the move, email authentication, testing restores) and wrote the skill in our own words. Not affiliated.

## Part of the Speedrun AI Labs Skill Library

Other skills at [speedrunlab.ai/#skills](https://www.speedrunlab.ai/#skills): [/reverse-engineer-anything-self-learning-loop](https://github.com/Speedrunlab/reverse-engineer-anything-self-learning-loop), [/reverse-engineer-anything](https://github.com/Speedrunlab/reverse-engineer-anything), [/hermes-agent-as-a-skill-file](https://github.com/Speedrunlab/hermes-agent-as-a-skill-file), [/deep-audit](https://github.com/Speedrunlab/deep-audit), [/read-before-write](https://github.com/Speedrunlab/read-before-write), [/no-slop-writing](https://github.com/Speedrunlab/no-slop-writing), [/no-slop-writing-au](https://github.com/Speedrunlab/no-slop-writing-au).

## Licence

MIT. Free to use, modify, and distribute.

*Built in Sydney by Speedrun AI Labs.*
