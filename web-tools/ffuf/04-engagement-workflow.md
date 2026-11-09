# 04 — Engagement Workflow

This is the "when and how in a real engagement" section — the part a command list alone doesn't cover.

## Where ffuf sits in the overall flow

```
Passive recon (subfinder, crt.sh, amass passive)
        ↓
Crawl / spider (katana, gau, waybackurls)
        ↓
Tech fingerprinting (httpx, whatweb, Wappalyzer)
        ↓
   ffuf  ← fills the gap: finds what the crawler can't see
        ↓
Manual verification / exploitation
        ↓
Reporting
```

ffuf is not a replacement for crawling — a crawler only finds what's *linked*. ffuf's job is to find what's **unlinked**: hidden directories, backup files, forgotten API versions, disabled-but-still-live endpoints, undocumented parameters.

## Phase-by-phase use

| Phase | ffuf technique |
|---|---|
| Recon | Subdomain/vhost fuzzing — expand attack surface before anything else |
| Mapping | Directory/file discovery, API route discovery — build the full map of reachable content |
| Testing | Parameter discovery, IDOR value fuzzing, 403/401 bypass, authenticated-vs-unauthenticated diffing |

## Decision flow when you land on a new target

1. Fingerprint the stack (don't fuzz blind).
2. Quick-pass `common.txt` + calibrate filters (see [01-core-syntax.md](01-core-syntax.md)).
3. Based on findings so far, pick the right wordlist tier (see [02-wordlist-selection.md](02-wordlist-selection.md)).
4. Run the targeted pass (dir/file, API, param — whichever the target type calls for).
5. Pull anything interesting into manual testing immediately rather than batching — a live session token or a live IDOR can expire or get rotated.
6. Only go to a deep/big wordlist pass if the standard pass justified the extra time/rate budget (unusual tech stack, custom app, high-value scope).

## Bug bounty vs. pentest differences

| | Bug bounty | Pentest |
|---|---|---|
| Rate | Conservative — many programs explicitly cap req/s or ban automated fuzzing outright; **read the program's rules before running anything** | Set per the Rules of Engagement (RoE) document — agreed `-rate`/`-t` and time window |
| Scope | Strict, program-defined, revisit before every session (scope changes) | Contract-defined, usually includes client notification requirements |
| Noise | Keep low — shared infra, other researchers, no SOC coordination | Client's SOC/WAF team is often expecting your traffic — confirm the time window |
| Reporting | Only what's exploitable — platforms deduct for noise/low-signal reports | Full methodology documented regardless of finding severity |

## Time-boxing

- Give the quick pass a hard stop (minutes) regardless of target size.
- Give the standard pass a budget tied to the rate limit, not an open-ended "run until done."
- Switch to manual testing as soon as you have enough leads to work — don't let fuzzing run in the background indefinitely "just in case."
- Deep/big wordlist passes are a deliberate decision, not a default — justify them (unusual stack, high scope value, time remaining in the engagement window).

## Finding validation (a ffuf hit is a lead, not a finding)

1. Re-request the hit manually (curl/Burp repeater) — confirm it's not a transient response or false positive from your filter.
2. Check what the endpoint actually returns/does — a `200` on a backup file is only interesting if it contains something; a `200` on an admin route is only interesting if it's not also behind a working auth check.
3. For IDOR-type hits: confirm the data belongs to a different principal than the authenticated session, not just a different response size.
4. For bypass-type hits: confirm the bypassed resource is genuinely sensitive, not a static asset that was never gated.
5. Capture evidence — Burp request/response pair, not just the ffuf CLI line — before moving on.

## From ffuf output to report

- Keep the raw `-o` output file as an engagement artifact (see [06-integration-output.md](06-integration-output.md)).
- In the report, state the technique (tool, wordlist tier, position fuzzed) briefly, then lead with the **validated** finding and its impact — not the fuzzing process itself.
- If a finding came from a custom/target-derived wordlist (JS-extracted paths, etc.), note that — it signals thoroughness and is usually not reproducible by a generic scanner, which matters for severity/uniqueness in bug bounty triage.
