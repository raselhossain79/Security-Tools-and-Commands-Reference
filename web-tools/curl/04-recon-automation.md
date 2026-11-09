# 04 — Recon & Automation

## Status code sweep over a list of URLs

```bash
while read -r url; do
  code=$(curl -s -o /dev/null -w '%{http_code}' "$url")
  echo "$code $url"
done < urls.txt
```

## Custom output format (`-w`) — key fields

```bash
curl -s -o /dev/null -w 'status=%{http_code} size=%{size_download} time=%{time_total}\n' https://target.tld
```

Useful fields: `%{http_code}`, `%{size_download}`, `%{time_total}`, `%{time_connect}`, `%{redirect_url}`, `%{num_redirects}`, `%{ssl_verify_result}`.

## Rate-limited loop (self-throttling, since curl has no built-in delay)

```bash
while read -r url; do
  curl -s -o /dev/null -w '%{http_code} %{url}\n' "$url"
  sleep 0.3
done < urls.txt
```

## Parallel requests (curl's own `--parallel`, modern curl versions)

```bash
curl --parallel --parallel-immediate -Z -o '#1.html' $(cat urls.txt | sed 's/^/ /')
```

For most pentest use, a controlled bash loop with `sleep` is preferable to raw parallelism — keeps you within RoE/program rate limits and makes correlating responses to requests easier.

## Chaining with recon tools

**Live host confirmation after subdomain enum:**
```bash
while read -r sub; do
  curl -s -o /dev/null -w '%{http_code} '"$sub"'\n' "https://$sub"
done < subdomains.txt | grep -v '^000'
```

**Quick header/tech fingerprint sweep:**
```bash
while read -r url; do
  echo "== $url =="
  curl -s -I "$url" | grep -iE 'server|x-powered-by|x-aspnet-version'
done < urls.txt
```

**Feed a URL list from `gau`/`waybackurls` into a status check:**
```bash
gau target.tld | while read -r url; do
  curl -s -o /dev/null -w '%{http_code} %{url}\n' "$url"
done
```

**Verifying a ffuf hit quickly (the most common handoff in practice):**
```bash
curl -i -s https://target.tld/path-ffuf-found | head -50
```

## Scripting a login → authenticated action sequence

```bash
curl -s -c cookies.txt https://target.tld/login > /dev/null
TOKEN=$(curl -s -b cookies.txt -c cookies.txt https://target.tld/login \
  -d 'user=testuser&pass=testpass' -w '%{http_code}' -o /dev/null)
echo "login status: $TOKEN"
curl -s -b cookies.txt https://target.tld/api/profile
```

Keep engagement-specific scripts like this in the engagement's own folder, not in this reference repo — they tend to contain target-identifying details and test credentials.
