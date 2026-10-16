# 8. Hash Identification Cheat Table

```
$1$...      MD5crypt (old Linux)      → hashcat -m 500  / john --format=md5crypt
$2$/$2a$    bcrypt                   → hashcat -m 3200  / john --format=bcrypt
$6$...      sha512crypt (Linux)       → hashcat -m 1800  / john --format=sha512crypt
$y$         yescrypt (modern Linux)   → john --format=crypt
NTLM        32 hex, Windows          → hashcat -m 1000  / john --format=NT
MD5         32 hex                    → hashcat -m 0
SHA1        40 hex                    → hashcat -m 100
SHA256      64 hex                    → hashcat -m 1400
WPA (cap)   need handshake            → hashcat -m 22000 (hcxpcapngtool) or aircrack-ng
```

`/etc/shadow` field format: `$id$salt$hash`

← [Back to index](README.md)
