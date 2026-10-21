# Tutorial: John the Ripper — password cracking (CPU)

**What it is:** fast, CPU-based password cracker. Works great in VMs where hashcat's GPU path fails — on an exam VM, John is often the *safer* bet.

Quick-start workflow:
```bash
# Linux password hashes: combine passwd + shadow first
unshadow /etc/passwd /etc/shadow > hashes.txt
sudo john --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt
sudo john --show hashes.txt         # see cracked results
```

## The 2john family (one format per file type)
```bash
zip2john locked.zip   > z.txt && john z.txt
rar2john locked.rar   > r.txt && john r.txt
ssh2john id_rsa       > s.txt && john s.txt
# also: keepass2john, pdf2john, office2john, pfx2john...
```
Any "crack this .zip/ssh key" exam question = `*2john` → `john`.

## Flags that matter
| Flag | Meaning |
|------|---------|
| `--wordlist=FILE` | dictionary attack (rockyou first!) |
| `--rules` / `--rules=All` | mutations: caps, digits, symbols appended |
| `--format=NAME` | force format: `NT`, `raw-md5`, `sha512crypt`, `bcrypt`, `crypt` |
| `--show FILE` | list cracked passwords (run *after* cracking) |
| `--incremental` | brute-force-ish mode (slow) |

## Exam-style examples
```bash
# Windows NTLM dump
john --format=NT --wordlist=rockyou.txt ntdump.txt

# Raw MD5 list from a SQLi dump
john --format=raw-md5 --wordlist=rockyou.txt md5.txt

# Same but with mutations (Password → Password1!)
john --wordlist=rockyou.txt --rules hashes.txt
```

## Gotchas
- rockyou ships gzipped: `sudo gunzip /usr/share/wordlists/rockyou.txt.gz` **first**.
- Wrong format → "no password loaded" or 0 candidates: identify the hash first ([08-hash-cheat-table.md](../08-hash-cheat-table.md)) and set `--format=`.
- John remembers progress — re-running a finished file shows nothing; `--show` (or a fresh copy of the hash file) reveals results.

**Deep dive:** [02-cryptography-password-cracking.md](../02-cryptography-password-cracking.md) · GPU alternative: [hashcat](06-hashcat.md)

← [Back to index](../README.md)
