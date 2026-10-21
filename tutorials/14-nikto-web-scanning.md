# Tutorial: Nikto — web server vulnerability scanner

**What it is:** a classic web-server scanner that checks for thousands of known misconfigurations: dangerous files, outdated software, default credentials pages, missing headers. **Remember: it's ACTIVE** — it sends hundreds of requests at the target (a favorite OSINT-vs-active exam trick, see [01-osint.md](../01-osint.md)).

Quick-start workflow:
```bash
nikto -h http://target.com
nikto -h 10.0.0.5 -p 80,8080          # multiple ports
nikto -h https://target.com           # or -p 443 with -ssl
```

## Reading the output
```
+ Target IP:          10.0.0.5
+ OSVDB-3092: /admin/: Admin login page found
+ Server leaks inodes via ETags
+ /backup.zip: Potentially interesting archive found
+ Apache/2.4.41 appears outdated (current is 2.4.58)
```
`+` lines = findings, sorted into: outdated software · default/dangerous files · info disclosures · misconfig headers.

## Flags that matter
| Flag | Meaning |
|------|---------|
| `-h` | target (host or URL) |
| `-p` | port(s) |
| `-ssl` | force SSL |
| `-o out.html` / `-o out.txt` | save the report |
| `-Tuning x` | scan only some test types (e.g. `-Tuning 9` = SQL injection probes only) |
| `-id admin:pass` | authenticate with basic-auth creds |

## Gotchas
- Nikto is **noisy** — it fills `access.log` with requests (its own scans show up in log-analysis questions).
- It finds *known* issues, not logic bugs: pair with [Gobuster](12-gobuster-web-discovery.md) (hidden paths), [sqlmap](07-sqlmap-web-attacks.md) (injection), and manual [Burp](08-burp-suite-web-attacks.md) testing.
- "No bugs found" doesn't mean the site is secure — read findings, verify interesting ones by hand.
- Passive first, active second: [whatweb](../01-osint.md) fingerprints without hammering the server; nikto hammers it.

← [Back to index](../README.md)
