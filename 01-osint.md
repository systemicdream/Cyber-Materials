# 1. Open Source Intelligence (OSINT)

## What it is
Collecting publicly available information about a target (people, domains, companies) without touching their systems. Passive — no packets sent to the target (usually).

## Key tools & commands

| Tool | Purpose | Example command |
|------|---------|-----------------|
| `theHarvester` | Emails, subdomains, hosts, names from public sources | `theHarvester -d example.com -b google,linkedin -l 500` |
| `sherlock` | Find usernames across social media sites | `sherlock johndoe` |
| `whois` | Domain registration info (registrar, nameservers, contacts) | `whois example.com` |
| `dig` / `nslookup` | DNS records (A, MX, TXT, NS) | `dig example.com ANY` · `dig axfr @ns1.example.com example.com` (zone transfer attempt) |
| `sublist3r` / `amass` | Subdomain enumeration | `sublist3r -d example.com` |
| `whatweb` | Web technology fingerprinting | `whatweb http://example.com` |
| `nikto` (active!) | Web server scanner — **active, not passive** | `nikto -h http://target.com` |
| `maltego` | Visual OSINT relationship mapping (GUI, needs account) |
| `recon-ng` | OSINT framework, module-based (like a passive Metasploit) |
| `exiftool` | Metadata from files/images (GPS, author, software) | `exiftool photo.jpg` |
| `google dorks` | Advanced search operators | `site:` `filetype:` `inurl:` `intitle:` `cache:` |
| `shodan` (web) | Internet-connected devices, open ports, banners |

## Classic Google dork examples
```
site:target.com filetype:pdf
site:target.com inurl:admin
intitle:"index of" "password"
site:target.com -www        (all subdomains except www)
filetype:env DB_PASSWORD    (leaked config files)
```

## OSINT exam tips
- Distinguish **passive** (whois, theHarvester, shodan) vs **active** (nikto, nmap) — this is a common test question.
- PGP key servers / `haveibeenpwned.com` can reveal breached emails.
- DNS zone transfer (`dig axfr`) is a *misconfiguration* to check for — usually denied on properly configured servers.

← [Back to index](README.md)
