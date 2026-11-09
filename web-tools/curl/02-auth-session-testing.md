# 02 — Auth & Session Testing

## Cookies

```bash
curl -b 'session=abc123' https://target.tld/app                    # send a cookie
curl -b 'session=abc123; other=value' https://target.tld/app       # multiple cookies
curl -c cookies.txt https://target.tld/login -d 'user=x&pass=y'    # save cookies returned by the server (cookie jar)
curl -b cookies.txt https://target.tld/app                          # reuse saved cookie jar on the next request
curl -b cookies.txt -c cookies.txt https://target.tld/app           # read AND update the jar (persists session across a script)
```

The jar workflow (`-c` then `-b cookies.txt -c cookies.txt`) is the standard way to script a login-then-act sequence without re-extracting the session value manually each time.

## Bearer token / API key auth

```bash
curl -H 'Authorization: Bearer <token>' https://target.tld/api/resource
curl -H 'X-API-Key: <key>' https://target.tld/api/resource
```

## HTTP Basic Auth

```bash
curl -u username:password https://target.tld/protected
curl -u username: https://target.tld/protected        # empty password — test if blank is accepted
```

## Testing session handling

```bash
# Confirm an endpoint actually requires auth
curl -i https://target.tld/api/profile                         # no auth — expect 401/403
curl -i -H 'Authorization: Bearer <token>' https://target.tld/api/profile   # with auth — expect 200

# Test token/session reuse after logout (session fixation / improper invalidation)
curl -i https://target.tld/logout -b cookies.txt
curl -i https://target.tld/api/profile -b cookies.txt    # should now fail — if it doesn't, that's a finding

# Test horizontal privilege — same session, different resource owner's object
curl -i -b cookies.txt https://target.tld/api/users/<other_user_id>/data
```

## JWT inspection (manual, no payload tampering here — just decode/inspect)

```bash
# Decode header and payload (base64url) to inspect claims, algorithm, expiry
echo '<jwt_header_b64>' | base64 -d 2>/dev/null; echo
echo '<jwt_payload_b64>' | base64 -d 2>/dev/null; echo
```

Check specifically for: `alg` field (is `none` accepted by the server?), token expiry (`exp`) actually enforced, and whether claims (role, user id) are trusted without signature validation server-side — confirm by modifying and resending, only within authorized scope.

```bash
curl -i -H 'Authorization: Bearer <modified_jwt>' https://target.tld/api/resource
```

## CSRF token handling

```bash
# Extract a CSRF token from a page, then use it in a subsequent POST
TOKEN=$(curl -s -c cookies.txt https://target.tld/form | grep -oE 'csrf_token" value="[^"]+"' | cut -d'"' -f3)
curl -i -b cookies.txt -d "csrf_token=$TOKEN&field=value" https://target.tld/submit
```

Useful to confirm whether the CSRF token is actually validated server-side (resend without it, or with a stale one, and compare).
