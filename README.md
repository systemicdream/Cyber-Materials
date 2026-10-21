# Cybersecurity 448 — Midterm Study Notes

> **Exam environment:** Kali Linux VM. Notes allowed (long version here on GitHub, paper cheat sheet on desk).
>
> **Topics:** (1) OSINT · (2) Cryptography & Password Cracking · (3) Log Analysis · (4) Network Traffic Analysis & Wireless Exploitation · (5) Network Scanning · (6) SQL Injection

## Table of Contents

| # | Topic | File |
|---|-------|------|
| 1 | Open Source Intelligence (OSINT) | [01-osint.md](01-osint.md) |
| 2 | Cryptography & Password Cracking | [02-cryptography-password-cracking.md](02-cryptography-password-cracking.md) |
| 3 | Log Analysis | [03-log-analysis.md](03-log-analysis.md) |
| 4 | Network Traffic Analysis & Wireless Exploitation | [04-network-traffic-wireless.md](04-network-traffic-wireless.md) |
| 5 | Network Scanning | [05-network-scanning.md](05-network-scanning.md) |
| 6 | SQL Injection | [06-sql-injection.md](06-sql-injection.md) |
| 7 | Command Line Survival Kit | [07-cli-survival-kit.md](07-cli-survival-kit.md) |
| 8 | Hash Identification Cheat Table | [08-hash-cheat-table.md](08-hash-cheat-table.md) |
| 9 | Lecture Concepts (W1–W8: crypto theory, foundations, architecture, networking, access control) | [09-lecture-concepts.md](09-lecture-concepts.md) |

## Quick exam-day checklist

- [ ] `gunzip` rockyou.txt FIRST
- [ ] Identify the hash before choosing a tool/format
- [ ] nmap scan before exploitation: `nmap -sV -sC -T4 target` (only targets in scope)
- [ ] aircrack: monitor mode → airodump (channel lock) → deauth → crack
- [ ] SQLi: `'` → `OR 1=1` → UNION column count → sqlmap if slow
- [ ] Logs: `grep "Failed password" | awk | sort | uniq -c | sort -rn`
- [ ] Wireshark filters: `ip.addr ==`, `http`, `eapol`, Follow TCP Stream
- [ ] Lecture questions: CIA triad, IAAA, SYN flood, Kerberos → [09-lecture-concepts.md](09-lecture-concepts.md)
- [ ] Read the whole question — hints are usually embedded
