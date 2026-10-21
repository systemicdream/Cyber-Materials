# 2. Cryptography & Password Cracking

## Classical / manual crypto (often done with CyberChef)
CyberChef (https://gchq.github.io/CyberChef) — browser tool, works offline, "Cyber Swiss Army Knife":

- **Base64** — ends in `=` padding, chars `A-Za-z0-9+/`
- **Hex** — only `0-9a-f`
- **ROT13 / Caesar** — letters shifted; brute force all 25 shifts in CyberChef
- **XOR** — CyberChef "XOR Brute Force" for unknown keys
- **Atbash** — alphabet reversed (A↔Z)
- **URL encoding** — `%20` = space, `%3D` = `=`
- **Morse / binary / decimal** conversions

**CyberChef workflow:** paste → try "Magic" operation (auto-detects encoding/encryption).

## Hash identification
| Hash | Length (hex chars) | Example/Notes |
|------|--------------------|---------------|
| MD5 | 32 | Fast, broken |
| SHA-1 | 40 | Broken |
| SHA-256 | 64 | Common |
| SHA-512 | 128 | |
| NTLM | 32 | Windows logins; looks like MD5 but context is Windows |
| bcrypt | 60, starts `$2a$` / `$2b$` / `$2y$` | Slow, salted, has cost factor |
| SHA-512crypt (`$6$`) | starts `$6$` | Linux `/etc/shadow` |
| MD5crypt (`$1$`) | starts `$1$` | old Linux shadow |
| yescrypt (`$y$`) | starts `$y$` | modern Linux shadow |

Use `hashid <hash>` or `hash-identifier` on Kali if unsure.

## Password generation / profiling
- `cupp` — **interactive Common User Password Profiler**: builds a personal wordlist from target details (name, pet, birthday, etc.)
```bash
cupp -i          # interactive mode
```
- `crunch` — generate wordlists from character sets
```bash
crunch 8 10 abc123 -o wordlist.txt     # lengths 8-10, charset abc123
crunch 5 5 0123456789 -t @@@12 -o wl.txt   # @ = placeholder for charset
```
- `cewl` — custom wordlist by spidering a website
```bash
cewl -d 2 -m 5 -w site_words.txt http://target.com
```

## Cracking tools

### John the Ripper (JtR) — great for raw hashes + shadow files
```bash
# Unshadow first for Linux password files
unshadow /etc/passwd /etc/shadow > hashes.txt
john hashes.txt                        # default wordlist mode
john --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt
john --show hashes.txt                 # view cracked
john --format=NT hashes.txt            # NTLM hashes

# Zip/Rar/Archive
zip2john file.zip > zip_hash.txt && john zip_hash.txt
rar2john file.rar > rar_hash.txt
# SSH keys
ssh2john id_rsa > ssh_hash.txt && john ssh_hash.txt

# Rules (mutations: caps, numbers, symbols appended)
john --wordlist=rockyou.txt --rules hashes.txt
```

### hashcat — GPU-accelerated, uses mode numbers
```bash
# Format: hashcat -m <mode> -a <attack> <hashfile> <wordlist>
hashcat -m 0  hashes.txt rockyou.txt       # MD5, dictionary
hashcat -m 1000 hashes.txt rockyou.txt     # NTLM
hashcat -m 1800 hashes.txt rockyou.txt     # sha512crypt ($6$)

# Attack modes: -a 0 = dictionary, -a 3 = brute force/mask, -a 1 = combinator
hashcat -m 0 -a 3 hashes.txt ?a?a?a?a?a?a   # mask: 6 any-chars

# Mask charset placeholders:
# ?l lowercase  ?u uppercase  ?d digit  ?s special  ?a all  ?b binary

# Show cracked
hashcat -m 0 hashes.txt --show

# Benchmark
hashcat -b

# If GPU/driver issues on a VM: fall back to CPU
hashcat -m 0 --force hashes.txt rockyou.txt   # (or just use john)
```

Common hashcat mode numbers to remember:
`0=MD5  100=SHA1  1400=SHA256  1700=SHA512  1000=NTLM  1800=sha512crypt  3200=bcrypt  22000=WPA-PBKDF2(PMKID/EAPOL)  16800=WPA-PMKID  2500=WPA/WPA2 old`

## Cracking strategy (exam workflow)
1. Identify hash type (length, prefix, context, `hashid`).
2. Try obvious wordlists: `rockyou.txt` (`/usr/share/wordlists/rockyou.txt` — must `gunzip` first on Kali: `sudo gunzip /usr/share/wordlists/rockyou.txt.gz`).
3. Try target-specific wordlist (cupp/cewl) — exam passwords often relate to the scenario.
4. Add rules / masks for mutations.
5. Consider the hint in the exam question — CTF-style exams usually embed hints.

← [Back to index](README.md) · [Hash ID cheat table →](08-hash-cheat-table.md)
