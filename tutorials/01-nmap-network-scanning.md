# Tutorial: nmap — network scanning

**What it is:** the standard port/service scanner. You'll use it on almost every exam scenario: "find live hosts, find open ports, find what's running."

Quick-start workflow:
```bash
nmap -sn 10.0.0.0/24          # 1. who's alive?
nmap -sV -sC -T4 10.0.0.5     # 2. what's running on the live host?
nmap -p- -sV 10.0.0.5         # 3. (only if needed) every port, with versions
```

## Reading the output
```
PORT     STATE SERVICE  VERSION
22/tcp   open  ssh      OpenSSH 8.2p1 Ubuntu
80/tcp   open  http     Apache httpd 2.4.41
443/tcp  filtered https   ← filtered = firewall in the way
```
- **open** = something is listening · **closed** = reachable, nothing there (RST) · **filtered** = no answer (firewall/ACL).

## The flags you'll actually use
| Flag | Meaning |
|------|---------|
| `-sn` | host discovery only (ping sweep, no port scan) |
| `-sS` / `-sT` / `-sU` | SYN (stealth, root) / full connect / UDP scan |
| `-sV` | probe service **versions** |
| `-O` | OS detection (needs root) |
| `-A` | aggressive = `-sV -O -sC --traceroute` |
| `-sC` | default safe scripts |
| `-p-` | all 65535 ports (slow) — or `-p 1-1000,8080` |
| `-Pn` | skip ping, assume host is up (blocked hosts) |
| `-T4` | speed (T1 slow/stealthy → T5 loud) |
| `-D RND:5` | decoy scan (hide your IP among 5 randoms) |
| `-oA base` | write normal + XML + grepable output |

## NSE scripts — where the exam points come from
```bash
ls /usr/share/nmap/scripts/ | grep smb       # browse by keyword
nmap --script ftp-anon 10.0.0.5 -p 21        # anonymous FTP allowed?
nmap --script smb-vuln* -p 139,445 10.0.0.5 # SMB vulns (EternalBlue etc.)
nmap --script http-enum 10.0.0.5             # hidden web paths
nmap --script vuln 10.0.0.5                   # run all vuln-category scripts
nmap --script "discovery,vuln" 10.0.0.5      # categories
```

## Gotchas
- Run as **root/sudo** or you lose `-sS`, `-O`, and raw-packet options.
- "All ports closed" → try `-Pn` and/or `-sU` (firewall may drop ping and UDP quietly).
- Only scan the exam's stated scope.

**Deep dive:** [05-network-scanning.md](../05-network-scanning.md) · feeds [searchsploit](13-searchsploit-vuln-research.md) via `-oX nmap.xml`

← [Back to index](../README.md)
