# 03 — Use Cases

## Directory / file discovery

```bash
ffuf -w raft-medium-directories.txt -u https://target.tld/FUZZ -mc 200,204,301,302,307,401,403 -t 40 -fc 404
```

Add extensions once the stack is fingerprinted:

```bash
ffuf -w raft-medium-files.txt -u https://target.tld/FUZZ -e .php,.bak,.old -mc 200,403 -t 40
```

## Recursive discovery

```bash
ffuf -w raft-medium-directories.txt -u https://target.tld/FUZZ -recursion -recursion-depth 2 -fc 404
```

## Subdomain enumeration

```bash
ffuf -w subdomains-top1million-5000.txt -u https://FUZZ.target.tld -fc 404 -t 50
```

## Virtual host (vhost) discovery

Same IP, different `Host:` header — useful when subdomain DNS doesn't resolve but the vhost still exists on the server:

```bash
ffuf -w subdomains-top1million-5000.txt -u https://target.tld -H 'Host: FUZZ.target.tld' -fs <baseline_size>
```

Calibrate `-fs` against the response size for a known-bad vhost first — vhost fuzzing almost always needs a size/word filter since status codes stay 200 across the board.

## GET parameter discovery

```bash
ffuf -w burp-parameter-names.txt -u 'https://target.tld/endpoint?FUZZ=test' -fc 404 -mc all -fs <baseline_size>
```

## POST parameter discovery

```bash
ffuf -w burp-parameter-names.txt -u https://target.tld/endpoint -X POST \
  -d 'FUZZ=test' -H 'Content-Type: application/x-www-form-urlencoded' \
  -fs <baseline_size>
```

## JSON body / API parameter fuzzing

```bash
ffuf -w params.txt -u https://target.tld/api/v1/resource -X POST \
  -d '{"FUZZ":"test"}' -H 'Content-Type: application/json' \
  -fc 400
```

## Value fuzzing (IDOR / numeric ID ranges)

Use `-w` with a generated numeric range file, or `--input-cmd` for on-the-fly ranges:

```bash
seq 1 5000 > ids.txt
ffuf -w ids.txt -u 'https://target.tld/api/users/FUZZ' -H 'Authorization: Bearer <token>' -mc 200 -fs <404_size>
```

Compare per-ID response size/content against the authenticated user's own object — a 200 with a *different* user's data at a sequential/neighboring ID is the actual IDOR signal, not just a 200 status.

## Authenticated fuzzing (session cookie / bearer token)

```bash
ffuf -w endpoints.txt -u https://target.tld/app/FUZZ \
  -H 'Cookie: session=<value>' -mc 200,403 -fc 404
```

Run the same wordlist unauthenticated in parallel to diff which endpoints only appear once logged in — flags access-control gaps worth manual review.

## 401/403 bypass fuzzing

Classic bypass payload list against a known-forbidden path, varying path, headers, and method:

```bash
ffuf -w 403-bypasses.txt -u https://target.tld/admin/FUZZ -mc all -fc 403
```

Also worth fuzzing manually alongside a wordlist:
- Path tricks: `/admin/`, `/admin/.`, `/admin//`, `/%2e/admin`, `/admin..;/`
- Header tricks: `X-Forwarded-For: 127.0.0.1`, `X-Original-URL: /admin`, `X-Rewrite-URL: /admin`
- Method tricks: `HEAD`, `POST` instead of `GET` on the same path

## Actuator / management endpoint discovery (Java/Spring targets)

```bash
ffuf -w spring-actuators.txt -u https://target.tld/FUZZ -mc 200,401,403 -fc 404
```

Look for `/actuator/env`, `/actuator/heapdump`, `/actuator/mappings` specifically — frequent source of config/secret leaks.
