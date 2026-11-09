# 05 — Security Testing Tricks

## Redirect inspection (open redirect / SSRF-adjacent checks)

```bash
curl -i https://target.tld/redirect?url=https://external.tld    # WITHOUT -L — see the raw Location header first
curl -s -o /dev/null -w '%{redirect_url}\n' https://target.tld/redirect?url=https://external.tld
```

Check whether the `Location` header reflects the input unvalidated, and whether curl without `-L` already shows the server accepted an external target — that's the evidence to capture before deciding whether to escalate (e.g. internal-range SSRF testing, only if explicitly in scope).

## Testing internal/metadata endpoints (SSRF confirmation — authorized scope only)

```bash
curl -i https://target.tld/fetch?url=http://169.254.169.254/latest/meta-data/
```

Only run this kind of check where SSRF is a confirmed or explicitly in-scope test objective — some programs treat cloud metadata probing as sensitive even when technically in scope, so check program rules first.

## Header injection / CRLF testing

```bash
curl -i -H $'X-Test: value\r\nX-Injected: true' https://target.tld/
```

Confirms whether the server/proxy splits on injected CRLF in a reflected header value — relevant for response-splitting and header-smuggling checks.

## Host header manipulation

```bash
curl -i -H 'Host: evil.tld' https://target.tld/
curl -i -H 'X-Forwarded-Host: evil.tld' https://target.tld/password-reset
```

Useful for password-reset poisoning checks and cache-poisoning via host header — confirm whether the app uses the header value to build links/tokens.

## TLS options (lab/engagement environments with self-signed or internal CAs)

```bash
curl -k https://internal-target.tld              # skip cert verification — only for authorized internal/lab targets, never as a habit on external scope
curl --cacert custom-ca.pem https://target.tld    # trust a specific CA instead of skipping verification entirely (preferred over -k when possible)
curl -v https://target.tld 2>&1 | grep -A5 'Server certificate'   # inspect the cert chain presented
```

Prefer `--cacert` with the actual internal CA over blanket `-k` where you can — keeps TLS validation meaningful and avoids masking a real cert issue.

## Forcing a specific TLS version/cipher (misconfiguration checks)

```bash
curl --tlsv1.0 -i https://target.tld     # confirm whether deprecated TLS versions are still accepted
curl --ciphers 'DEFAULT:@SECLEVEL=0' -i https://target.tld
```

## Proxying through Burp for manual follow-up

```bash
curl -x http://127.0.0.1:8080 -k https://target.tld
```

Route any curl request you want to inspect/modify interactively through Burp this way, rather than rebuilding it from scratch in Repeater.

## Capturing clean evidence for a report

```bash
curl -v https://target.tld/vulnerable-endpoint 2>&1 | tee evidence-request-response.txt
```

`-v` output combined with `tee` gives a single file with both the exact request sent and the response received — attach directly to the report rather than paraphrasing what was sent.

## Checking security headers quickly

```bash
curl -s -D - -o /dev/null https://target.tld | grep -iE 'strict-transport-security|content-security-policy|x-frame-options|x-content-type-options|referrer-policy|permissions-policy'
```

Missing headers here are a quick, low-effort finding worth noting even when the main engagement focus is elsewhere.
