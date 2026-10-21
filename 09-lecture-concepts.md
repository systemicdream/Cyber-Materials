# 9. Lecture Concepts (W1–W8)

> Everything below is distilled from the lecture/anki material — the conceptual half of the exam that the hands-on files (01–08) don't cover.

## W1 — Cryptography theory

**Vocabulary:** cryptography = "secret writing" · plaintext → ciphertext · cryptanalysis = breaking codes (famous: Alan Turing, WWII).

**Kerckhoffs-style principle:** the *algorithm* should never be secret; only the **key** needs secrecy. Public algorithms get more scrutiny and trust.

**Cryptanalysis attack types:**
| Attack | You have | Goal |
|--------|----------|------|
| Ciphertext-only | ciphertext only | find plaintext |
| Known-plaintext | ciphertext + plaintext | find the key |
| Chosen-plaintext | you pick plaintexts, get ciphertexts | find key via patterns |

Finding the key matters because the same key likely encrypts future traffic.

**Symmetric (= single-key / classical):** one key encrypts AND decrypts.
- **Transposition** ciphers rearrange letters (rail fence: two columns, read across). Ciphertext keeps **normal English frequencies** (E,T,A,O,I common; J,Q,X,Z rare; digrams TH, ER, ON).
- **Substitution** ciphers replace letters. Ciphertext has **abnormal frequencies**.
- **Caesar:** key = shift (typically 3, max 25). Break via frequency analysis.
- **Vigenère:** Caesar with a repeating keyword (A=0…Z=25). Weakness: repeating patterns reveal key length; **longer key = stronger** (fewer repeats).
- Stronger: **combine substitution + transposition** — basis of modern layered encryption.
- **Enigma** (WWII): flaw = a letter can never encrypt to itself; German phrase reuse (weather reports) gave known plaintext.

**Key count formula (symmetric, n people):** `n(n−1)/2` — 3 people → 3 keys; 1000 → 499,500. This is the scalability problem.

**Asymmetric (= public key):** Diffie & Hellman, 1976. Encrypt with recipient's **public** key, decrypt with **private** key. Encrypt with your *private* key = **digital signature** (verified with your public key). Sign + keep confidential: encrypt with sender's private key, then recipient's public key; recipient reverses the order.
- Fixes: key exchange/distribution, scalability. Downside: **slow** (~1000×).
- **Hybrid / TLS:** asymmetric used **once** to exchange a symmetric session key; symmetric carries the traffic.
- **RSA** (Rivest–Shamir–Adleman, MIT): one-way function — multiplying two big primes is fast, **factoring** the product takes enormously longer. Bigger primes = bigger gap = stronger. Not "unbreakable," just *computationally infeasible*.
- Predecessors: James Ellis (concept), Clifford Cocks (RSA-like) — classified British work.

## W2 — Cracking theory, no-tech hacking, malware history

**Password storage:** pre-2000 hashes in `/etc/passwd`; now in `/etc/shadow` (root-only). Password never stored in cleartext — hash + **salt** (prefix making identical passwords hash differently).
- Dictionary attack: fast, only cracks words in the list. Brute force: guaranteed but slow (could take a lifetime).
- rockyou.txt ≈ **14 million** entries, `/usr/share/wordlists/`, ships gzipped.
- hashcat `-m` = hash mode, `-a` = attack mode (0=straight/dictionary, 3=mask, 6=hybrid append, 7=hybrid prepend). Brute-force mode is deprecated — **mask** replaces it (same keyspace, reducible). Masks: `?l`=26 lower, `?d`=10 digits, `?a`=95 all. One extra mask char multiplies keyspace by the alphabet size (×95 for `?a`). Custom set: `--custom-charset1=abc…` → `?1`.
- Wrong `-m` → "Line length exception" or instant-separator error. Potfile (`~/.hashcat/hashcat.potfile`) stores cracked hashes; `--show` displays them.

**No-tech hacking (Johnny Long, "I'm Johnny. I hack stuff"):** security broken without technology — **dumpster diving**, **shoulder surfing**, **tailgating/piggybacking**, **social engineering**. Weakest link is people, not tech.

**Malware history (Mikko Hypponen):** **Brain** = first PC virus, 1986, boot-sector, by Basit & Amjad (Pakistan; authors' names in the code). Early viruses = hobbyists, announced themselves; modern malware = **organized criminal gangs, for profit, hides itself** (banking trojans, keyloggers, selling infected PCs; moving between jurisdictions).
- **Stuxnet** (Ralph Langner's TED talk): first worm targeting **industrial control (PLCs)**; target = **Natanz uranium enrichment (Iran) only**. Entered via **infected USB**, spread via USB/network shares; hunted for the **PLC-programming PC** (Siemens **Step 7**), then rewrote the PLC download — operator's screen showed correct code, pump ran forever (balloon exploded in the Symantec demo). Payload = two warheads: rotor control (spin up/down to crack rotors) + valve manipulation (6 cascades). **164** = centrifuges per cascade. Hid sabotage by feeding **prerecorded sensor data** (like a heist looping security-camera footage). Generic payload → "template for cyber weapon of mass destruction." Langner: the US led it ("the cyber superpower"), Mossad involved.

## W4 — Security foundations

**Infosec (NIST):** protection preserving **Confidentiality, Integrity, Availability** — the **CIA triad**:
- Confidentiality — vs eavesdropping/data theft (protects PII, IP: source code, patents, formulas)
- Integrity — vs corruption/tampering (example: attackers altered Apple's iOS compiler so malware was injected into apps built with it)
- Availability — vs denial/data loss

Must be **balanced**, not maximized: a dark, unplugged room has perfect C & I but zero A.

**Principles:** least privilege / need-to-know · separation of duties (no one controls a transaction end-to-end) · rotation of duties (periodically change assignments).

**Attacks/concepts:** masquerader (pretending to be a real user) · Trojan horse (legit-looking file carrying a keylogger) · DoS/DDoS (tie up a system) · **botnet** = network of compromised **zombie** computers · social engineering (Kevin Mitnick, *The Art of Deception*) · phone phreaking (Captain Crunch whistle = **2600 Hz** tone) · Steve Wozniak = greatest all-around hacker (hardware+software, Apple).
- History: "hacker" was originally **positive** (clever experimenter).
- **Patch Tuesday** → attackers reverse-engineer patches to hit unpatched systems.
- People are the problem: found USB drives (Stuxnet trick), phishing, CEO workstations (highest privileges, lowest technical skill).
- Unencrypted wireless (coffee shop) = cleartext traffic for everyone (unless HTTPS).
- Telnet/FTP insecure = cleartext credentials. nmap = the unauthorized probing tool. AV = most-used security tech; biometrics = least used.
- Defender-attacker asymmetry: **defender must close ALL ways in; attacker needs only ONE**. "A good attack is one the engineers never thought of" — Schneier.
- Robert Morris' 3 golden rules: don't own a computer; don't power it on; don't use it.
- Security = insurance (hard to price protection against events that may never happen); usually an afterthought because it slows shipping.

## W6 — Security architecture & OS

**Architecture:** security view of the system; **baked in from the start**, not bolted on. Layers: applications → utilities → OS → hardware (networking beneath). Higher layers use primitives of lower ones; **only the OS touches hardware**. Security primitives go as **low as possible** — access to a lower level lets you change the security of levels above.

**OS functions:** GUI + hardware resource control, plus program creation/execution, I/O, file system, error handling, and **accounting** (logs of who connected, what ran, privilege changes). Multitasking (programs take turns) · multithreading (run a process's other threads while one waits) · multiprocessing (parallel processors) · **SMP** = shared memory, one OS · **MPP** = per-processor memory · **clustering** = separate computers working on one task (Google: off-the-shelf = cheap).

**OS security vulnerabilities:**
- **Context switch** → **storage residue**: RAM not scrubbed between processes — a lower-privilege process can read a higher one's leftovers (usernames/passwords).
- **TOC/TOU** (time-of-check/time-of-use): parameter validated, then changed by another process before use. Mitigation: keep check and use adjacent.
- **Buffer overflow:** write past the buffer into adjacent memory → attacker's code executes with the area's higher privileges. Defense: **base + limit registers** + privilege modes on memory areas.
- **Core dump:** crash writes RAM/registers to disk — sensitive data readable unless secured.
- Virtual memory: swapped pages persist on disk after power-off (Windows: swap on a secured partition; Linux: **encrypt** swap). Paging exposes decrypted data — use a **memory lock** to keep encrypted data from being paged.
- **Interrupts:** interrupt vector table maps interrupts to handlers. Risks: gains-interrupts-privilege (insecure transfer-back leaves elevated status), **interrupt redirection** (change handler address → OS runs malicious code; original handler unchanged → hard to detect). Viruses abuse interrupts for privilege escalation.
- **Trusted Computing Base (TCB):** process activation, domain switching, memory protection, I/O — the components that must be trusted to stay within bounds.

**Security models:**
- Military policy: objects get classifications (unclassified < confidential < secret < top secret), subjects get clearances; read only at-or-below your clearance.
- **Bell-LaPadula** = cornerstone multilevel security model, built as a **state machine** (all states + all transitions defined; secure initial state + secure transitions ⇒ every reachable state is secure).
- **Zachman Framework:** 1980s enterprise-architecture framework — how/what/where/who/when/why; also define behaviors that *shouldn't* happen so anomalies stand out.

## W7 — Networking

**TCP:** connection-oriented; **3-way handshake = SYN → SYN-ACK → ACK**. **SYN flood** = send SYNs, never ACK → server holds half-open connections until ports exhausted. **UDP:** connectionless, no handshake, no delivery guarantee — fast; good for audio/video (streaming adjusts for loss).

**ICMP** (Internet Control Message Protocol): ping = test reachability; ping sweep = find live hosts. Admins may suppress ping replies (hide from sweeps; downside: looks down).

**IPv4:** 32 bits, 4 octets × 8 bits (max 255 each). Subnet mask 255.255.0.0 = /16 network bits. **IPv6:** 128 bits, hex, colons; built-in **IPSec**; not fully adopted because private IPs + **NAT** stretched IPv4.

**Private IPv4 ranges:** `10.0.0.0/8` · `172.16–172.31` · `192.168.0.0/16` — never seen on the public internet. **NAT** translates internal private IPs to the router's public IP; security benefit: attackers can't see internal IPs.

**Ports:** SSH 22 (SCP + SFTP run over SSH, port 22) · HTTP 80, HTTPS 443 (TLS) · FTP 21 control / 20 data · FTPS = FTP over TLS (990) · TFTP = trivial, over **UDP 69** (fine for small files — lost packets undetected) · SMTP = sending email (SSL/TLS on 465) · LDAPS 636. Email retrieval: **IMAP4** (stays on server, multi-client) vs **POP3** (downloads, removes from server, offline-friendly).
- **SSL → TLS:** browser requests ID → server sends certificate (server's **public key**) → browser checks CA trust, expiry, revocation, common-name match → browser encrypts a session key with the public key → server decrypts with **private key** → rest of session uses the **symmetric session key** (asymmetric only at the start).
- **VPN:** IPSec encrypts gateway-to-gateway; puts the remote user directly on the internal network.

**Firewalls:** purpose = block unwanted inbound traffic; **exception** = explicit allow rule; **default-deny** = anything not allowed is blocked; rules processed **top-down, first match wins**, so `deny any any` goes **last**. Host-based (on a machine) vs network-based (between internal segments) vs **border** (edge; can block outbound too, e.g. phishing sites). Anti-spoofing: deny inbound packets with private source addresses.
- Generations: 1st = packet filtering · 2nd = **stateful inspection** · 3rd = **WAF** (blocks XSS, SQLi; a.k.a. application gateway) · **NGFW/UTM** = everything bundled (firewall, AV, IPS, content filtering…).

**DMZ:** internet-facing servers (web, email, public files) live in the DMZ; sensitive internal stuff (HR, card data) does NOT. Internet never touches the internal network directly — attacker must breach a second firewall. DMZ web server reaches internal DB only via a specific firewall exception.

**Proxy server:** internal clients connect *through* it; it allows/denies per user group's rules.

## W8 — Access control

**IAAA:** **I**dentification (assert identity — username, badge; never encode job function like "admin123" in a username) · **Au**thentication (prove it) · **Au**thorization (what may you access) · **A**ccountability (logs tie actions to individuals).

**Authentication factors:** know (password — weakest) / have (ATM card, token) / are (fingerprint, iris, typing rhythm).
- Username + password = **one** factor (both "know"). ATM card + PIN = **two**-factor. NIST: 2FA now required, especially admin/sensitive access.
- RSA-style token: code from time + seed + algorithm, changes ~every 30 s; server computes the same.
- Smart card chip / NFC tap: **one-time code per transaction** (magnetic stripe sends the same data every swipe).
- Biometrics: retina = blood vessels *inside* the eyeball; iris = granularity patterns in the colored part. Deploy for accuracy, speed (<10 s), acceptability.
- Errors: **Type 1 = false reject** (legit user denied); **Type 2 = false accept** (intruder gets in) — minimize Type 2. **Crossover error** = FAR + FRR (lower is better). Tighten → fewer accepts but more rejects; the two trade off.

**Authorization types:** **rule-based** (per-user ACLs) · **role-based** (create role, assign users, grant to role — easier to manage) · **content-dependent** (access depends on data values — manager sees own department, HR sees all).
- Authentication = *who you are*; authorization = *what you may do*.

**SSO:** authenticate once, reach all permitted resources. Pros: one strong password, single point to administer. Con: stolen creds = keys to the kingdom.

**Kerberos** (MIT, 1980s; used by Active Directory): 3 parties = client, resource, **KDC** (domain controller).
- Password never crosses the network — it's the shared secret encrypting the authenticator, which the KDC decrypts with the stored password.
- **TGT**: ticket encrypted with a key only the KDC knows; client reuses it to get service tickets. Kept in the **Kerberos tray** (special memory, never swapped to disk).
- Service tickets encrypted with the *resource server's* key — server decrypts with its own password; client can't read or tamper with them.
- Tickets have **time limits** (hours) — stops replay, forces revalidation.
- Benefits: password never transmitted, central control, transparent after login. Three heads = authentication, accounting/authorization, auditing.

← [Back to index](README.md)
