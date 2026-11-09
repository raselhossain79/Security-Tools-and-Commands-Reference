# curl Cheat Sheet

## Basics
```bash
curl -i https://target.tld
curl -s -o /dev/null -w '%{http_code}\n' https://target.tld
curl -v https://target.tld
```

## Methods & data
```bash
curl -X POST https://target.tld/ep -d 'key=value'
curl -X PUT https://target.tld/ep -H 'Content-Type: application/json' -d '{"key":"value"}'
curl -X DELETE https://target.tld/ep
curl -G -d 'key=value' https://target.tld/ep
```

## Headers
```bash
curl -H 'Authorization: Bearer <token>' https://target.tld
curl -D - -o /dev/null -s https://target.tld     # headers only
```

## Auth
```bash
curl -u user:pass https://target.tld
curl -b 'session=abc' https://target.tld
curl -c jar.txt -b jar.txt https://target.tld     # persist session across calls
```

## Redirects
```bash
curl -i https://target.tld           # see raw Location, no follow
curl -L https://target.tld           # follow
```

## File upload
```bash
curl -F 'file=@path.jpg' https://target.tld/upload
```

## Proxy through Burp
```bash
curl -x http://127.0.0.1:8080 -k https://target.tld
```

## TLS
```bash
curl -k https://target.tld                  # skip verification (lab only)
curl --cacert ca.pem https://target.tld      # trust specific CA
curl --tlsv1.0 -i https://target.tld         # test deprecated TLS
```

## Timing / custom output
```bash
curl -s -o /dev/null -w 'code=%{http_code} size=%{size_download} time=%{time_total}\n' https://target.tld
```

## Evidence capture
```bash
curl -v https://target.tld 2>&1 | tee evidence.txt
```

## Security headers quick check
```bash
curl -s -D - -o /dev/null https://target.tld | grep -iE 'strict-transport-security|content-security-policy|x-frame-options'
```

## Method coverage sweep
```bash
for m in GET POST PUT PATCH DELETE OPTIONS; do
  echo -n "$m: "; curl -s -o /dev/null -w '%{http_code}\n' -X "$m" https://target.tld/ep
done
```

Full reference: [01-core-syntax.md](01-core-syntax.md) · [05-security-testing-tricks.md](05-security-testing-tricks.md)
