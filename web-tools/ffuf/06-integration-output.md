# 06 — Integration & Output

## Burp Suite integration

**Replay via Burp proxy** (lets you watch/intercept ffuf traffic in Burp, or route it through Burp's upstream settings):

```bash
ffuf -w wordlist.txt -u https://target.tld/FUZZ -replay-proxy http://127.0.0.1:8080
```

**Run from a raw request file** saved out of Burp (right-click → Copy to file / Save item):

```bash
ffuf -w wordlist.txt -request request.txt -request-proto https
```

Edit the saved request file and replace the value you want fuzzed with `FUZZ` before running.

**Send matches to Burp** (requires the matcher/replay setup above) — useful to pivot straight from a ffuf hit into Repeater for manual validation.

## Output formats

```bash
ffuf -w wordlist.txt -u https://target.tld/FUZZ -o results.json -of json
```

Supported formats: `json`, `ejson`, `html`, `md`, `csv`, `all`.

- Use `json` for anything you intend to parse/chain into other tools.
- Use `md` or `html` for a quick human-readable artifact to attach to notes/reports.
- Use `csv` when handing results to non-technical stakeholders or spreadsheets.

## Parsing JSON output with `jq`

```bash
# List just URLs that returned 200
jq -r '.results[] | select(.status==200) | .url' results.json

# Status + length + url, tab separated
jq -r '.results[] | "\(.status)\t\(.length)\t\(.url)"' results.json
```

## Recon tool chaining

**Subdomain → live host check → ffuf:**
```bash
subfinder -d target.tld -silent | httpx -silent | tee live.txt
ffuf -w subdomains.txt -u https://FUZZ.target.tld -fc 404
```

**Crawl → JS path extraction → ffuf custom wordlist (see 02-wordlist-selection.md):**
```bash
katana -u https://target.tld -jc -silent > urls.txt
grep -oE '"/[a-zA-Z0-9/_-]+"' urls.txt | tr -d '"' | sort -u > js-paths.txt
ffuf -w js-paths.txt -u https://target.tld/FUZZ -fc 404
```

**ffuf → feed live URLs into a vuln scanner (e.g. nuclei):**
```bash
jq -r '.results[] | select(.status==200) | .url' results.json | nuclei -silent
```

Keep this chaining pattern consistent with the recon/OSINT notes elsewhere in this repo so the wordlist/tool references stay aligned.
