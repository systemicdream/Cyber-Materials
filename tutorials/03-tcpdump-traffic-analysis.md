# Tutorial: tcpdump — CLI packet capture

**What it is:** Wireshark's terminal-only cousin. Perfect when you only have a shell (SSH box, no GUI exam VM) or want a quick capture with zero clicks.

Quick-start workflow:
```bash
sudo tcpdump -i eth0 -n              # watch live traffic, no DNS resolution
sudo tcpdump -i eth0 -w out.pcap      # capture to a file (open later in Wireshark)
tcpdump -r out.pcap -nn              # read a capture back
```

## The flags that matter
| Flag | Meaning |
|------|---------|
| `-i eth0` | interface (`-i any` = all) |
| `-n` / `-nn` | don't resolve hostnames / also port names |
| `-w file.pcap` | write raw packets to file |
| `-r file.pcap` | read a capture |
| `-A` | print payload as ASCII (spot cleartext creds) |
| `-X` | hex + ASCII |
| `-c 100` | stop after 100 packets |
| `port 80`, `host 10.0.0.5`, `not arp` | BPF capture filters (combine with `and`/`or`) |

## Exam-style examples
```bash
# Who is this box talking to?
sudo tcpdump -i eth0 -n | head -50

# Watch for cleartext passwords on FTP/Telnet
sudo tcpdump -i eth0 -A port 21 or port 23

# Capture DNS only, save it
sudo tcpdump -i eth0 -n port 53 -w dns.pcap

# Show one host's HTTP traffic as readable text
tcpdump -r cap.pcap -A host 10.0.0.5 and port 80
```

## Gotchas
- BPF filter syntax ≠ Wireshark display-filter syntax: here it's `tcp port 80`, not `tcp.port == 80`.
- You usually need `sudo` (raw socket access).
- A `-w` file is raw packets — it looks like garbage in `cat`; read it with `-r` or Wireshark.

**Related:** [Wireshark](02-wireshark-traffic-analysis.md) · [04-network-traffic-wireless.md](../04-network-traffic-wireless.md)

← [Back to index](../README.md)
