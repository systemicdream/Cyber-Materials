# Tutorial: hashcat — password cracking (GPU)

**What it is:** GPU-accelerated cracker. Faster than John when the GPU works, but on class VMs it often needs a fallback — know both tools.

Quick-start workflow:
```bash
# 1. Identify the hash (length / $prefix$) → mode number
#    $6$...  → sha512crypt → -m 1800
hashcat -m 1800 hashes.txt /usr/share/wordlists/rockyou.txt
hashcat -m 1800 hashes.txt --show      # view cracked results
```

## The command shape
```
hashcat -m <mode> -a <attack> <hashfile> <wordlist-or-mask>
```
| Piece | Meaning |
|-------|---------|
| `-m` | hash mode (algorithm) — see table below |
| `-a 0` | dictionary attack |
| `-a 3` | mask (brute-force-style) attack |
| `-a 6` / `-a 7` | hybrid: mask appended / prepended to wordlist |
| `--show` | print cracked pairs |
| `--force` | ignore warnings, run anyway (VM fallback) |

## Mode numbers worth memorizing
```
MD5=0  SHA1=100  SHA256=1400  SHA512=1700  NTLM=1000
md5crypt=500  sha512crypt($6$)=1800  bcrypt($2$)=3200
WPA-PBKDF2/PMKID=22000
```

## Masks (attack mode -a 3)
```
?l lowercase 26 · ?u uppercase 26 · ?d digit 10 · ?s special · ?a all 95
hashcat -m 0 -a 3 hash.txt ?d?d?d?d?d?d            # 6-digit PIN
hashcat -m 0 -a 3 hash.txt --custom-charset1=abc ?1?1?1
```
Every extra `?a` multiplies the keyspace by ~95 — keep masks tight.

## Gotchas
- "Line length exception" / separators on target hashes → wrong `-m` mode; re-identify.
- hashcat skips hashes already in its **potfile** (`~/.hashcat/hashcat.potfile`); delete it to redo a run.
- VM with no GPU → `--force`, or just switch to [John](05-john-the-ripper.md) — same rockyou, less pain.
- Dictionary attack needs the **gzipped rockyou decompressed first**.

**Deep dive:** [02-cryptography-password-cracking.md](../02-cryptography-password-cracking.md) · hash IDs: [08-hash-cheat-table.md](../08-hash-cheat-table.md)

← [Back to index](../README.md)
