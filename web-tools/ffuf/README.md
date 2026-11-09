# ffuf — Fuzz Faster U Fool

Reference and methodology notes for using `ffuf` in authorized web/API penetration testing and bug bounty engagements.

> ffuf is a HTTP fuzzer: it sends a wordlist of payloads into a request (URL path, query/POST parameter, header, or value) and evaluates the responses. It is not a scanner on its own — it needs a target position, a wordlist, and a filtering strategy to be useful.

## Folder index

| File | Purpose |
|---|---|
| [01-core-syntax.md](01-core-syntax.md) | FUZZ keyword, fuzzing modes, matchers/filters, calibration |
| [02-wordlist-selection.md](02-wordlist-selection.md) | Which wordlist for which scenario — decision matrix |
| [03-use-cases.md](03-use-cases.md) | Directory/file discovery, vhost/subdomain, parameter, IDOR, API, auth, 403 bypass |
| [04-engagement-workflow.md](04-engagement-workflow.md) | Where ffuf fits in a real engagement, decision flow, validation |
| [05-performance-waf-safety.md](05-performance-waf-safety.md) | Rate, threads, timeouts, WAF handling, scope safety |
| [06-integration-output.md](06-integration-output.md) | Burp integration, output formats, recon tool chains |
| [cheatsheet.md](cheatsheet.md) | One-page command reference |

## Rules of engagement (read before every run)

- Only run against targets explicitly in scope, with written authorization (bug bounty program scope page or signed pentest contract).
- Respect the program's/client's rate-limit and out-of-scope rules (some programs explicitly disallow automated fuzzing, or cap requests/sec).
- Never fuzz production payment, auth-lockout, or account-deletion endpoints without an agreed rate and a fallback account.
- Log what you ran (command + timestamp) — needed for the report and for the client to correlate with their own logs/WAF alerts.
- A ffuf hit is a *lead*, not a finding. Always manually verify before reporting (see [04-engagement-workflow.md](04-engagement-workflow.md)).

## Quick start

```bash
ffuf -w /path/to/wordlist.txt -u https://target.tld/FUZZ -mc 200,204,301,302,307,401,403 -t 40
```

Then open [01-core-syntax.md](01-core-syntax.md) for calibration before trusting the output.
