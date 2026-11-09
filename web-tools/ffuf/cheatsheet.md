# ffuf Cheat Sheet

## Basics
```bash
ffuf -w wordlist.txt -u https://target.tld/FUZZ
ffuf -w wordlist.txt:MYKEY -u https://target.tld/MYKEY
```

## Calibration
```bash
ffuf -w common.txt -u https://target.tld/FUZZ -ac
ffuf -w common.txt -u https://target.tld/FUZZ -fc 404 -fw 1234
```

## Directory / file discovery
```bash
ffuf -w raft-medium-directories.txt -u https://target.tld/FUZZ -mc 200,204,301,302,307,401,403 -t 40 -fc 404
ffuf -w raft-medium-files.txt -u https://target.tld/FUZZ -e .php,.bak,.old -mc 200,403
```

## Recursion
```bash
ffuf -w wordlist.txt -u https://target.tld/FUZZ -recursion -recursion-depth 2
```

## Subdomain / vhost
```bash
ffuf -w subs.txt -u https://FUZZ.target.tld -fc 404
ffuf -w subs.txt -u https://target.tld -H 'Host: FUZZ.target.tld' -fs <baseline_size>
```

## Parameters (GET/POST/JSON)
```bash
ffuf -w params.txt -u 'https://target.tld/ep?FUZZ=test' -fs <baseline>
ffuf -w params.txt -u https://target.tld/ep -X POST -d 'FUZZ=test' -H 'Content-Type: application/x-www-form-urlencoded' -fs <baseline>
ffuf -w params.txt -u https://target.tld/api -X POST -d '{"FUZZ":"test"}' -H 'Content-Type: application/json' -fc 400
```

## Value fuzzing / IDOR
```bash
seq 1 5000 > ids.txt
ffuf -w ids.txt -u 'https://target.tld/api/users/FUZZ' -H 'Authorization: Bearer <token>' -mc 200 -fs <404_size>
```

## Auth header/cookie
```bash
ffuf -w endpoints.txt -u https://target.tld/app/FUZZ -H 'Cookie: session=<val>' -mc 200,403 -fc 404
```

## Raw request from Burp
```bash
ffuf -w wordlist.txt -request request.txt -request-proto https
```

## Proxy through Burp
```bash
ffuf -w wordlist.txt -u https://target.tld/FUZZ -replay-proxy http://127.0.0.1:8080
```

## 403 bypass
```bash
ffuf -w 403-bypasses.txt -u https://target.tld/admin/FUZZ -mc all -fc 403
```

## Rate control
```bash
ffuf -w wordlist.txt -u https://target.tld/FUZZ -rate 8 -p "0.1-0.3"
ffuf -w wordlist.txt -u https://target.tld/FUZZ -maxtime 600
```

## Output
```bash
ffuf -w wordlist.txt -u https://target.tld/FUZZ -o results.json -of json
jq -r '.results[] | select(.status==200) | .url' results.json
```

## Matchers / filters quick reference
| Code size | word | line | regex | header |
|---|---|---|---|---|
| `-mc`/`-fc` | `-mw`/`-fw` | `-ml`/`-fl` | `-mr`/`-fr` | `-mh`/`-fh` |
| status | size: `-ms`/`-fs` | | | |

Full reference: [01-core-syntax.md](01-core-syntax.md) · [03-use-cases.md](03-use-cases.md)
