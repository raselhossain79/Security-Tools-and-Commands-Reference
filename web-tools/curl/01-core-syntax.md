# 01 — Core Syntax

## Basic request

```bash
curl https://target.tld
curl -i https://target.tld          # include response headers
curl -s https://target.tld           # silent (no progress meter)
curl -sS https://target.tld          # silent but still show errors
```

## HTTP methods

```bash
curl -X GET https://target.tld/api/resource
curl -X POST https://target.tld/api/resource
curl -X PUT https://target.tld/api/resource
curl -X DELETE https://target.tld/api/resource
curl -X PATCH https://target.tld/api/resource
curl -X OPTIONS -i https://target.tld/api/resource   # check Allow header for supported methods
```

## Headers

```bash
curl -H 'X-Custom-Header: value' https://target.tld
curl -H 'Authorization: Bearer <token>' -H 'Accept: application/json' https://target.tld/api
```

Multiple `-H` flags stack. Check response headers specifically:

```bash
curl -s -D - -o /dev/null https://target.tld   # dump headers only, discard body
```

## Sending data

```bash
curl -d 'key=value&key2=value2' https://target.tld/endpoint          # form-encoded, implies POST
curl -d @data.txt https://target.tld/endpoint                         # data from file
curl -G -d 'key=value' https://target.tld/endpoint                    # force as GET query string instead
curl --data-raw 'raw body, no @ file expansion' https://target.tld
```

## Verbose / debug output

```bash
curl -v https://target.tld            # request + response headers, TLS handshake summary
curl -vvv https://target.tld           # maximum verbosity
curl --trace-ascii trace.txt https://target.tld   # full trace to file, useful for report evidence
```

`-v` is the first thing to reach for when a request behaves unexpectedly — shows exactly what was sent, including headers curl adds automatically (`User-Agent`, `Accept`, etc.).

## Following redirects

```bash
curl -L https://target.tld            # follow redirects
curl -L --max-redirs 5 https://target.tld
curl -i https://target.tld            # WITHOUT -L: see the redirect itself (Location header) — do this first when testing redirect behavior
```

## Saving output

```bash
curl -o response.html https://target.tld
curl -O https://target.tld/file.zip    # save using remote filename
```

## Common useful flags

| Flag | Purpose |
|---|---|
| `-i` | Include response headers in output |
| `-s` / `-sS` | Silent / silent-but-show-errors |
| `-v` | Verbose (request + response + TLS) |
| `-L` | Follow redirects |
| `-X` | HTTP method |
| `-H` | Add header |
| `-d` / `--data-raw` | Send body data |
| `-o` / `-O` | Save output to file |
| `-k` / `--insecure` | Skip TLS certificate verification (lab/engagement use only — see 05) |
| `-A` | Set custom User-Agent |
| `-e` | Set Referer header |
| `-b` / `-c` | Read / write cookies (see 02) |
| `-w` | Custom output format (status code, timing, size — see cheatsheet) |
