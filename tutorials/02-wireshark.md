# Tutorial: Wireshark — traffic analysis

**What it is:** GUI packet analyzer. On exams you get a `.pcap` and questions like "what password was sent?" or "was there a scan?"

Quick-start workflow:
1. `wireshark capture.pcap` (or `sudo wireshark` to capture live).
2. Type a display filter in the top bar.
3. Right-click a packet → **Follow → TCP Stream** to read a full conversation.

## Display filters (the exam bread-and-butter)
```
ip.addr == 10.0.0.5          # any traffic with this host
http                          # all HTTP
http.request.method == "POST" # logins/credential submissions
http.request.uri contains "login"
ftp || telnet                 # cleartext protocols (creds in the clear!)
eapol                         # WPA 4-way handshake frames
tcp.flags.syn == 1 && tcp.flags.ack == 0   # SYN scan / flood signature
dns                           # what did this host look up
```

## Finding credentials in a capture (classic exam task)
```
http.request.method == "POST"
```
→ Follow the TCP stream of a POST → form data shows `user=admin&pass=hunter2`.
FTP/Telnet are even easier: every keystroke (including login) is in cleartext.

## Pulling files out of a capture
**File → Export Objects → HTTP** → pick the file → Save. No manual reassembly needed.

## Statistics tools people forget
- **Statistics → Conversations:** who talked to whom (spot the attacker IP fast).
- **Statistics → Protocol Hierarchy:** what's inside at a glance.
- **Statistics → Endpoints:** busiest hosts.

## Capture filters vs display filters (common confusion)
- **Capture filters** (BPF, set *before* capturing): `tcp port 80`, `host 10.0.0.5`.
- **Display filters** (applied after): `tcp.port == 80`, `ip.addr == 10.0.0.5` — lowercase names, `==` syntax. Don't mix the syntaxes up.

**Deep dive:** [04-network-traffic-wireless.md](../04-network-traffic-wireless.md) · CLI alternative: [tcpdump](03-tcpdump.md)

← [Back to index](../README.md)
