# 3. Log Analysis

## Where logs live (Linux)
```
/var/log/auth.log      # SSH logins, sudo, authentication (Debian/Kali)
/var/log/syslog        # general system
/var/log/apache2/access.log & error.log   # web server
/var/log/nginx/access.log & error.log
/var/log/faillog / btmp  # failed logins
/var/log/wtmp (last), /var/log/utmp (who)
/var/log/dmesg         # kernel/boot
journalctl            # systemd logs: journalctl -u ssh --since "1 hour ago"
```

## Essential commands
```bash
# See failed SSH attempts (brute force evidence)
grep "Failed password" /var/log/auth.log
grep "Failed password" /var/log/auth.log | wc -l      # count them

# Successful logins
grep "Accepted password" /var/log/auth.log

# Unique attacking IPs
grep "Failed password" /var/log/auth.log | awk '{print $NF}' | sort | uniq -c | sort -rn

# Who logged in, when
last                 # reads wtmp
lastb                # failed logins (btmp)

# Tail follow
tail -f /var/log/apache2/access.log

# Cut relevant fields
grep "sshd" /var/log/auth.log | grep -i "invalid user"
```

## What attack signatures look like
| Attack | Log signature |
|--------|--------------|
| SSH brute force | Many `Failed password for X from IP` in short time |
| Success after brute force | `Accepted password` right after failures |
| Web SQL injection attempt | URLs with `'`, `UNION SELECT`, `1=1`, `%27` in access.log |
| Port scan | Many different destination ports from one IP, short intervals; SYN with no follow-up (`flags [S]`, no `ACK` back) |
| Directory brute force | Bunch of 404s, one or few 200s from same IP |
| Log4Shell | `${jndi:ldap://...}` strings in requests |

## awk/sed one-liners that save exams
```bash
awk '{print $1}' file              # first field (e.g., IP from apache log)
awk '{print $9}' access.log | sort | uniq -c    # status codes histogram
awk '$9 == 500' access.log         # only 500 errors
sed 's/^.*from //' log             # strip everything before "from"
cut -d: -f2 /etc/shadow            # 2nd colon-separated field
sort | uniq -c | sort -rn          # THE frequency pattern (memorize!)
```

Apache/Nginx combined log format (field positions):
```
$1=IP  $3=user  $4=[date/time]  $5-6=request  $7=status...
# Actually: $1 IP, $4 [time], $6 "METHOD /path HTTP/1.1", $9 status, $10 size
awk '{print $9}' access.log        # status code
awk '{print $1}' access.log       # source IP
```

← [Back to index](README.md) · [CLI survival kit →](07-cli-survival-kit.md)
