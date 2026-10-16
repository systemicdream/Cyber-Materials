# 7. Command Line Survival Kit

## Everyday commands
```bash
grep -i pattern file       # case-insensitive
grep -v pattern file       # INVERT match (exclude)
grep -c                    # count
grep -rn "text" dir/       # recursive with line numbers
wc -l file                 # count lines
sort | uniq -c | sort -rn  # top frequencies
head -n 20 / tail -n 20
awk '{print $1}' / cut -d: -f1
find / -name "*.pcap" 2>/dev/null
strings file               # readable text inside binary (great for forensics)
xxd file | head            # hex dump
base64 -d / base64 file
iconv / openssl
sudo gunzip /usr/share/wordlists/rockyou.txt.gz    # CRITICAL pre-step on Kali!
sudo apt install <tool>
which <tool> / whereis
history | grep nmap
```

## Network basics
```bash
ip a / ifconfig            # interfaces & IPs
ip route                   # gateway
sudo iw dev wlan0 link     # wireless link info
iwconfig                   # wireless config (old)
netstat -tulnp / ss -tulnp # listening ports & processes
ping / traceroute
sudo service network-manager restart
```

## binwalk / file analysis
```bash
binwalk file                      # embedded files/signatures in firmware/images
binwalk -e file                   # extract embedded files
strings file | less
file file                         # identify file type
xxd file
```

← [Back to index](README.md)
