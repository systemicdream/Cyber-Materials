# Tutorial: Hydra — online brute-forcing (logins over the network)

**What it is:** network login brute-forcer. **John/hashcat crack files offline; Hydra actually knocks on the login service** (SSH, FTP, web logins). Know which situation calls for which — exams love that distinction.

Quick-start workflow:
```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.0.0.5 ssh
#    ^single user      ^password list           ^target ^service
#    (-L users.txt for a list of usernames)
```
Success prints: `[22][ssh] host: 10.0.0.5 login: admin password: hunter2`

## Syntax shape
```
hydra -l <user|-L userfile> -p <pass|-P passfile> <target> <service>
```

## Supported services (the common ones)
`ssh` · `ftp` · `telnet` · `rdp` · `smb` · `mysql` · `vnc` · `http-get` · `http-post-form` · `pop3` · `imap`

## Web login form (the tricky syntax — memorize the shape)
```bash
hydra -l admin -P rockyou.txt 10.0.0.5 http-post-form \
  "/login.php:username=^USER^&password=^PASS^:Login failed"
```
Three parts inside the quotes, split by `:`:
1. the form **path**
2. the **POST body** — `^USER^` and `^PASS^` are where the candidates go
3. the **failure message** (`F=` marker): when it's absent, you're in

## Gotchas
- Services with **lockout policies** will lock accounts — check what you're hitting.
- `-t 4` limits threads; `-V` shows each attempt; `-f` stop on first success.
- Wrong failure string in `http-post-form` = either every password "works" or none do — the string must match the page's actual error text.
- ssh/login also possible via [Metasploit](09-metasploit-exploitation.md) `auxiliary/scanner/ssh/ssh_login`.

← [Back to index](../README.md)
