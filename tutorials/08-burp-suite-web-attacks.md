# Tutorial: Burp Suite — web traffic interception

**What it is:** an intercepting proxy for web traffic. You browse *through* Burp, so you can pause, inspect, and replay every request — the gateway tool for manual SQLi and for building `sqlmap -r` requests.

## One-time setup (do this first!)
1. **Burp → Proxy → Options:** default listener is `127.0.0.1:8080` — leave it.
2. **Browser:** set HTTP proxy to `127.0.0.1:8080` (use FoxyProxy extension to toggle it fast).
3. **Browser → http://burp** → download + install the **CA certificate** (else HTTPS sites show errors).

## The tabs you'll actually use
| Tab | What for |
|-----|----------|
| **Proxy** | live traffic; **Intercept On** pauses requests before they're sent |
| **Repeater** | resend one request with edits — *the* tool for manual SQLi |
| **Intruder** | brute-force / fuzz a parameter (basic 30-day only) |
| **Decoder** | encode/decode Base64, URL, hex — mini CyberChef |
| **Comparer** | diff two responses (blind SQLi response differences) |

## Classic exam workflow: catch a request for sqlmap
1. Intercept **On** → submit the login form once.
2. In the intercept view: see the raw request —
```
POST /login.php HTTP/1.1
Host: target.com
...

username=admin&password=test
```
3. Right-click → **Send to Repeater** → tweak the payload (try `admin'-- -`) → **Go** → read the response.
4. Right-click → **Save item** → save `request.txt` → `sqlmap -r request.txt --batch`.

## Tips
- **Intercept Off** unless you need it — otherwise every click freezes the browser.
- Everything you browse through the proxy is logged (Target → Site map).
- **OWASP ZAP** is the free/open alternative — same proxy + Repeater concepts, different names.

**Next steps:** [sqlmap](07-sqlmap-web-attacks.md) · manual payloads: [06-sql-injection.md](../06-sql-injection.md)

← [Back to index](../README.md)
