# Tutorial: Gobuster — directory & content brute-forcing

**What it is:** brute-forces **hidden paths** on a web server (`/admin`, `/backup.zip`, `/api`) against a wordlist. The classic web "what else is on this server?" exam step.

Quick-start workflow:
```bash
gobuster dir -u http://target.com -w /usr/share/wordlists/dirb/common.txt
```
Results show status: `200/301` (found!) vs `403` (exists, forbidden) vs the wall of `404`s (misses).

## The modes
| Mode | Finds |
|------|-------|
| `dir` | hidden **directories/files** (the default choice) |
| `dns` | subdomains (`gobuster dns -d target.com -w subdomains.txt`) |
| `vhost` | virtual hosts |
| `fuzz` | arbitrary parameter positions |

## Flags that matter
| Flag | Meaning |
|------|---------|
| `-u` | target URL |
| `-w` | wordlist |
| `-x php,html,txt,zip` | also try extensions (`-x php` finds `/login.php`) |
| `-t 50` | threads (speed) |
| `-k` | ignore TLS cert errors (self-signed HTTPS targets) |
| `-o out.txt` | save results |
| `--exclude-length 123` | hide spam responses of a fixed size |

## Exam-style example
```bash
gobuster dir -u http://10.0.0.5 -w /usr/share/wordlists/dirb/common.txt \
  -x php,html,txt,zip -t 50 -o found.txt
# → /admin.php (200), /uploads (301), /backup.zip (200)
```

## Sibling tools
- `dirb http://target.com` — older, same job
- `ffuf -u http://target.com/FUZZ -w common.txt` — modern, `FUZZ` marks the spot
- `dirsearch -u http://target.com` — convenient defaults

Tip: after finding a hidden page, note its **status codes and files** — that feeds [sqlmap](07-sqlmap-web-attacks.md), [Burp](08-burp-suite-web-attacks.md), and log-analysis questions (dirbusting in `access.log` = wall of 404s then a 200 — see [03-log-analysis.md](../03-log-analysis.md)).

← [Back to index](../README.md)
