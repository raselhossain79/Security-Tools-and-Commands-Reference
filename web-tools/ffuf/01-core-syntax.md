# 01 — Core Syntax

## The FUZZ keyword

`FUZZ` is a placeholder you place anywhere in the request — URL path, query string, POST body, header name/value, or cookie. ffuf substitutes it with each wordlist line.

```bash
ffuf -w wordlist.txt -u https://target.tld/FUZZ
ffuf -w wordlist.txt -u 'https://target.tld/api?id=FUZZ'
ffuf -w wordlist.txt -u https://target.tld/ -H 'X-Custom-Header: FUZZ'
ffuf -w wordlist.txt -u https://target.tld/ -d '{"username":"FUZZ","password":"test"}' -H 'Content-Type: application/json'
```

A custom keyword is useful once you combine multiple wordlists against different positions:

```bash
ffuf -w users.txt:USER -w passwords.txt:PASS \
  -u https://target.tld/login -X POST \
  -d 'username=USER&password=PASS' \
  -H 'Content-Type: application/x-www-form-urlencoded'
```

## Multi-wordlist modes (`-mode`)

- `clusterbomb` (default): every combination of all wordlists (Cartesian product). Use for credential spraying across users × passwords.
- `pitchfork`: wordlists are stepped through in parallel, line N of list 1 with line N of list 2. Use when lists are pre-paired (e.g. known username:password pairs).
- `sniper`: fuzzes one keyword position at a time while keeping others static — useful for multi-parameter payload testing.

## Matchers vs filters

Matchers (`-m*`) **keep** responses that match a condition. Filters (`-f*`) **drop** responses that match a condition. You generally pick one approach, not both, for the same signal.

| Flag | Meaning |
|---|---|
| `-mc` / `-fc` | Match/filter by HTTP status code (e.g. `-mc 200,301,403`) |
| `-ms` / `-fs` | Match/filter by response size (bytes) |
| `-mw` / `-fw` | Match/filter by word count |
| `-ml` / `-fl` | Match/filter by line count |
| `-mr` / `-fr` | Match/filter by regex on response body |
| `-mh` / `-fh` | Match/filter by response header |
| `-ac` | Auto-calibrate filters (sends baseline requests and derives a filter automatically) |

Prefer word count (`-fw`) over size when the response includes variable content (timestamps, CSRF tokens) — size will drift per request even for a "not found" page, word count usually won't.

## Calibration workflow (do this before trusting any result)

Most false positives come from skipping this step — a target that returns `200 OK` with a generic "not found" page for everything will otherwise look like 100% hits.

1. **Request a known-invalid path first** to see the baseline "not found" response:
   ```bash
   curl -s -o /dev/null -w '%{http_code} %{size_download}\n' https://target.tld/this-should-not-exist-xyz123
   ```
2. **Run ffuf without filters** on a small/common wordlist and observe the dominant status code, size, and word count — that's almost certainly the "soft 404" pattern, not real hits.
3. **Filter the dominant pattern out**, then re-run:
   ```bash
   ffuf -w common.txt -u https://target.tld/FUZZ -fc 404 -fw 1234
   ```
4. Or let ffuf do it automatically:
   ```bash
   ffuf -w common.txt -u https://target.tld/FUZZ -ac
   ```
   `-ac` is convenient but not infallible on targets with multiple soft-404 patterns (e.g. different "not found" pages per subpath) — spot-check its filter choice.
5. **Re-calibrate per target section.** A soft-404 baseline on `/` doesn't always hold under `/api/` or `/admin/` — recalibrate when you pivot into a new area of the app.

## Recursion

```bash
ffuf -w wordlist.txt -u https://target.tld/FUZZ -recursion -recursion-depth 2 -e .php,.bak
```

Automatically re-runs ffuf against any directory it discovers, up to the given depth. Useful for quick mapping, but multiplies request volume fast — combine with a tight filter and a sane `-recursion-depth` (2–3), and prefer doing this manually/selectively on larger scopes to stay within rate limits.
