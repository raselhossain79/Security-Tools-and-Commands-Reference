# 02 — Wordlist Selection

This is the part that actually decides whether a ffuf run finds anything. The wrong wordlist against the right target wastes time and rate-limit budget; the right wordlist against a fingerprinted stack finds things a generic crawler never will.

## Sources

- **SecLists** — https://github.com/danielmiessler/SecLists (the default starting point; `Discovery/Web-Content/`, `Discovery/DNS/`, `Fuzzing/`)
- **Assetnote wordlists** — https://wordlists.assetnote.io/ (generated from real crawl/response data — stronger signal for APIs, parameters, and modern web-app routes than hand-built lists)
- **Custom, target-derived lists** — built from the target itself (see below) — usually the highest hit rate in a real engagement

Verify exact paths when you install — SecLists reorganizes folders between versions.

## Decision matrix

| Scenario | Wordlist | Notes |
|---|---|---|
| Quick first pass, any target | `SecLists/Discovery/Web-Content/common.txt` | Minutes, not thorough — just a sanity sweep |
| Standard directory/file discovery | `raft-medium-directories.txt`, `raft-medium-files.txt` | Good balance of coverage vs. time |
| Deep directory/file discovery | `directory-list-2.3-medium.txt` (or `-big.txt`), Assetnote `web_content` lists | Reserve for in-scope, time-boxed targets |
| API endpoint discovery | `api-endpoints.txt` (SecLists), Assetnote `httparchive_apiroutes_*` | Combine with the app's own JS-derived routes (below) |
| Parameter discovery (GET/POST) | `burp-parameter-names.txt`, Assetnote `parameters_top_1m` | Pair with `-mode sniper` if testing params one at a time against a known-good baseline request |
| Subdomain / vhost — quick | `subdomains-top1million-5000.txt` | First pass |
| Subdomain / vhost — deep | `subdomains-top1million-110000.txt`, Assetnote subdomain lists | When scope allows wider enumeration |
| Backup / config / sensitive files | `SecLists/Discovery/Web-Content/` backup + common extensions (`.bak`, `.old`, `.swp`, `.zip`, `.git/`, `.env`, `.DS_Store`) | Pair with `-e` extension flag rather than a bloated list |
| Login/credential testing (authorized only) | Known or program-permitted credential lists, never generic "common passwords" against live auth without explicit authorization | High risk of lockouts/alerts — confirm this is in scope first |

## Tech-stack based selection

Fingerprint the stack first (headers, error pages, `Wappalyzer`/`whatweb`, JS framework signatures), then pick extensions/lists accordingly:

| Stack | `-e` extensions | Notes |
|---|---|---|
| PHP | `.php,.phtml,.php.bak,.old` | Also check `.php~` (editor swap files) |
| ASP.NET | `.aspx,.asmx,.ashx,.config` | `web.config` backups are a classic find |
| Java (Spring/Struts) | `.jsp,.do,.action,.jsa` | Check actuator/management endpoints separately (see 03) |
| Node/Express, modern SPA/API | often no extension — route-based | Use API/route wordlists, not file-extension lists |
| Static/unknown | `.html,.txt,.json,.xml` | Low-cost pass while fingerprinting continues |

## Size tiers (time-boxing)

- **Quick (minutes):** `common.txt`, top-5000 subdomain list — always run this first on every new target.
- **Standard (10–30 min depending on rate limit):** raft-medium lists, Assetnote medium sets.
- **Deep (30 min+):** big/full lists — only when scope is wide, time allows, and the standard pass found enough signal (unusual tech, custom routes) to justify it.

## Building a custom, target-derived wordlist (usually the highest-yield step)

This consistently outperforms generic lists in real engagements because it reflects the target's own naming conventions:

1. **Extract paths from JS files** served by the target:
   ```bash
   katana -u https://target.tld -jc | grep -oE '"/[a-zA-Z0-9/_-]+"' | tr -d '"' | sort -u > js-paths.txt
   ```
2. **Pull historical URLs** from Wayback/gau/OTX:
   ```bash
   gau target.tld | unfurl paths | sort -u > wayback-paths.txt
   ```
3. **`cewl`** the target's own pages for company/product-specific terms — useful for naming-convention-based admin panels, internal tool names.
4. **Merge and dedupe** with the base wordlist to create a target-specific set:
   ```bash
   cat js-paths.txt wayback-paths.txt raft-medium-directories.txt | sort -u > target-custom.txt
   ```

Keep these per-target custom lists in the engagement's own folder, not in this repo — they may contain client-identifying paths.
