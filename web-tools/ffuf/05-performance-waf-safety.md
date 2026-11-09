# 05 — Performance, WAF Handling & Safety

## Core throughput flags

| Flag | Meaning |
|---|---|
| `-t` | Number of concurrent threads (default 40) |
| `-rate` | Max requests/second (overrides raw thread speed — use this for RoE-mandated limits) |
| `-p` | Delay between requests, supports jitter: `-p "0.1-0.5"` |
| `-timeout` | Per-request timeout in seconds |
| `-maxtime` | Hard stop for the whole run (seconds) |
| `-maxtime-job` | Hard stop per recursive job |

## Setting rate for the context

```bash
# Bug bounty, conservative, program caps at ~10 req/s
ffuf -w wordlist.txt -u https://target.tld/FUZZ -rate 8 -p "0.1-0.3"

# Pentest, RoE-approved higher rate on internal/staging
ffuf -w wordlist.txt -u https://target.tld/FUZZ -t 100 -rate 150
```

Always default to the conservative setting first run, then increase only if the RoE/program explicitly allows and the target's error rate stays flat.

## Detecting you're hitting a WAF/rate-limit mid-run

Signs:
- A sudden flood of identical status codes (often `429`, `403`, or a captcha-page `200`) across otherwise-varied paths.
- Response times climbing sharply.
- Your own IP getting blocked (test with a plain `curl` outside ffuf if results flatline).

Response:
```bash
ffuf -w wordlist.txt -u https://target.tld/FUZZ -rate 5 -p "1-3"
```
Drop rate drastically, add jitter, and consider spacing the run further or rotating through an approved proxy list if the engagement permits it. Never attempt to evade a WAF as a way to continue fuzzing scope you weren't cleared to hit at that volume — if the client/program's own protection is blocking you, that's a signal to slow down, not route around it, unless WAF bypass is itself an explicit, in-scope test objective.

## Scope safety

- Double-check the `-u` target against the current scope definition before every run — scope changes (especially in bug bounty) and a stale target list is a common cause of out-of-scope testing.
- Avoid wordlists or payloads that could trigger destructive actions (e.g. fuzzing a `DELETE`-capable endpoint with a large ID range) unless explicitly cleared.
- Be deliberate with `-X` on state-changing methods (`POST`, `PUT`, `DELETE`) during parameter/value fuzzing — a large wordlist against a live `POST /api/orders` endpoint can create real side effects.
- Use `-mc all -fc <known-404>` carefully — `all` includes every status, including ones you didn't expect (e.g. `500` triggering expensive backend operations repeatedly).

## Logging your own run

Keep a simple run log (command + timestamp + target) for every session — needed to correlate with client/program logs if anything is flagged, and useful for your own report writing later:

```bash
echo "$(date -u +%FT%TZ) $(history 1)" >> ffuf-run-log.txt
```
