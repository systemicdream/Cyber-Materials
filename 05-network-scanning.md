# 5. Network Scanning

## nmap — the star of the show
```bash
nmap -sn 10.0.0.0/24                  # ping sweep / host discovery, no port scan
nmap 10.0.0.5                          # default: top 1000 TCP ports
nmap -p- 10.0.0.5                      # all 65535 ports (slow!)
nmap -p 1-1000,8080 10.0.0.5           # port range + extra

# Scan types
nmap -sS target        # SYN (stealth/half-open) — needs root, default as root
nmap -sT target        # full TCP connect (no root)
nmap -sU target        # UDP (slow — needs root)
nmap -sA target        # ACK scan (map firewall rules)

# Detection/depth
nmap -sV target        # service/version detection
nmap -O target         # OS detection
nmap -A target         # aggressive = -sV -O -sC --traceroute (great default)
nmap -sC target        # default scripts (safe)

# Timing (T0 slowest/stealthy → T5 fastest/noisy)
nmap -T4 -A -sV target     # the classic combo

# NSE scripts
nmap --script vuln target
nmap --script ftp-anon target
nmap --script smb-vuln* -p 139,445 target
nmap --script http-enum target
ls /usr/share/nmap/scripts/ | grep <keyword>

# Output files
nmap -oN normal.txt -oX out.xml -oG grepable.txt target
# -oN normal, -oG greppable, -oX XML, -oA all three

# Other host discovery (no ping):
nmap -Pn target         # skip host discovery (treat as up) — good if ping is blocked
nmap -PE/-PP/-PM        # ICMP echo/timestamp/netmask discovery
nmap -PS22,80 -PA80     # TCP SYN/ACK discovery probes
```

## Scan state meanings
| State | Meaning |
|-------|---------|
| open | app accepting connections |
| closed | port reachable but no service (RST) |
| filtered | firewall/ACL blocking (no response or unreachable) |
| unfiltered | reachable but nmap can't classify (ACK scan) |

## Other scanning tools
```bash
netdiscover -r 10.0.0.0/24          # ARP-based host discovery (L2, same subnet)
arp-scan --localnet                 # ARP sweep
masscan -p80,443 10.0.0.0/24 --rate=1000   # ultra fast port scan
hping3 -S -p 80 target              # packet crafting / SYN flood basics
```
Metasploit also has `db_nmap` and auxiliary scanners:
```
use auxiliary/scanner/portscan/tcp
use auxiliary/scanner/smb/smb_version
use auxiliary/scanner/ssh/ssh_version
```

## Exam key facts
- SYN scan = "stealthy" because it never completes the 3-way handshake (sends RST after SYN/ACK).
- UDP scan is slow because closed UDP ports should return ICMP unreachable, and lossy.
- `-sV` identifies *versions* of services — pair with `-O` for full fingerprint.
- Firewall evidence: "filtered" ports.
- Remember: only scan targets you're authorized to (exam range).

← [Back to index](README.md)
