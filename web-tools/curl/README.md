# curl — Pentest & Bug Bounty Reference

Practical `curl` reference for web/API penetration testing and bug bounty work — manual verification, API probing, auth/session testing, and lightweight recon/automation.

> Unlike `ffuf`, curl isn't a dedicated fuzzing/scanning tool — it's the thing you reach for *constantly*: to verify a ffuf hit, replay a request with a tweak, script a quick loop, or confirm a finding with clean evidence for a report.

## Folder index

| File | Purpose |
|---|---|
| [01-core-syntax.md](01-core-syntax.md) | Methods, headers, data, core flags, verbose/debug output |
| [02-auth-session-testing.md](02-auth-session-testing.md) | Cookies, bearer tokens, Basic Auth, JWT handling |
| [03-api-testing.md](03-api-testing.md) | JSON/XML bodies, content-type tricks, REST verbs, file upload |
| [04-recon-automation.md](04-recon-automation.md) | curl in bash loops, parallel requests, chaining with other recon tools |
| [05-security-testing-tricks.md](05-security-testing-tricks.md) | Redirect/SSRF verification, header injection checks, TLS options, Burp proxying, evidence capture |
| [cheatsheet.md](cheatsheet.md) | One-page flag reference |

## Rules of engagement (same as the ffuf notes)

- Only against in-scope, authorized targets.
- Respect rate limits — curl has no built-in throttling, so when scripting loops, add your own delay (see [04-recon-automation.md](04-recon-automation.md)).
- Keep `-v`/`-o` output as evidence for anything you report — raw request/response pairs are the strongest proof in a writeup.
- This repo does not include attack payload lists (SQLi/XSS/SSRF strings) — that belongs in the vulnerability-specific notes, not here. These files cover technique and syntax only.
