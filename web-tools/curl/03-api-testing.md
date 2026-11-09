# 03 — API Testing

## JSON body

```bash
curl -X POST https://target.tld/api/resource \
  -H 'Content-Type: application/json' \
  -d '{"key":"value","nested":{"field":123}}'
```

## From a file (cleaner for large/complex bodies)

```bash
curl -X POST https://target.tld/api/resource \
  -H 'Content-Type: application/json' \
  -d @payload.json
```

## XML body

```bash
curl -X POST https://target.tld/api/resource \
  -H 'Content-Type: application/xml' \
  -d '<request><field>value</field></request>'
```

## Content-Type mismatch testing

Useful to check if the server trusts the declared type over actually parsing it, or accepts an unexpected type:

```bash
curl -X POST https://target.tld/api/resource -H 'Content-Type: text/plain' -d '{"key":"value"}'
curl -X POST https://target.tld/api/resource -H 'Content-Type: application/json' -d 'key=value'   # wrong format for declared type
```

## REST verb coverage check

Run the same endpoint across verbs to map what's actually implemented vs. documented:

```bash
for m in GET POST PUT PATCH DELETE OPTIONS HEAD; do
  echo "== $m =="
  curl -s -o /dev/null -w '%{http_code}\n' -X "$m" https://target.tld/api/resource
done
```

A `405 Method Not Allowed` vs `404` vs `200` distinguishes "exists but blocked" from "doesn't exist" from "accessible" — useful for finding under-protected verbs (e.g. `DELETE` allowed where only `GET` was expected).

## File upload

```bash
curl -X POST https://target.tld/upload -F 'file=@/path/to/file.jpg'
curl -X POST https://target.tld/upload -F 'file=@payload.php;type=image/jpeg;filename=shell.php'   # content-type / extension mismatch testing, authorized scope only
```

## GraphQL

```bash
curl -X POST https://target.tld/graphql \
  -H 'Content-Type: application/json' \
  -d '{"query":"{ __schema { types { name } } }"}'    # introspection check
```

If introspection is enabled in production, that itself is often worth flagging — confirm it's not intentional (dev/staging misconfigured as prod-reachable).

## Pagination / rate-limit probing

```bash
for i in $(seq 1 20); do
  curl -s -o /dev/null -w '%{http_code} '  https://target.tld/api/resource?page=$i
done; echo
```

Watch for `429` onset point, and whether rate-limit headers (`X-RateLimit-Remaining`, `Retry-After`) are present and honest.

## Response timing (useful for blind/time-based checks, auth timing attacks)

```bash
curl -s -o /dev/null -w 'time_total: %{time_total}\n' https://target.tld/login -d 'user=admin&pass=x'
```
