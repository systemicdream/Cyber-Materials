# Tutorial: Searchsploit — offline exploit database search

**What it is:** command-line search over the entire Exploit-DB archive — a *local copy* of the world's public exploit database. The bridge between "nmap says Apache 2.4.41" and "here's the working exploit."

Quick-start workflow:
```bash
searchsploit apache 2.4
searchsploit openssh 8.2
searchsploit eternalblue
```
Each hit shows: `EDB-ID | Title | Path` → the path points at a ready-made exploit script under `/usr/share/exploitdb/exploits/`.

## Flags that matter
| Flag | Meaning |
|------|---------|
| `searchsploit -t <term>` | search **titles only** (cleaner results) |
| `-m <EDB-ID>` | **mirror** (copy) the exploit into your current folder to use/edit |
| `-x <id>` | examine a copy in `$PAGER` |
| `--nmap file.xml` | search for exploits matching an **nmap XML output** (killer feature) |
| `-u` | update the database (`sudo searchsploit -u`) |

## The nmap pairing (exam gold)
```bash
sudo nmap -sV -oX scan.xml 10.0.0.5
searchsploit --nmap scan.xml
# → matches every detected service/version against known exploits
```
Then pull the winner and inspect it before running:
```bash
searchsploit -m 42315.py
less 42315.py        # always read: does it need options? is it a PoC or weaponized?
```

## Gotchas
- Match the **exact version** where possible: an exploit for "Apache 2.4.49" may not touch 2.4.41.
- Many results are Python/PHP scripts: check the header comments for usage (`python3 exploit.py <target> <port>`).
- If the box has internet, you can also browse exploit-db.com directly, but offline `searchsploit` always works — even in isolated exam VMs.

**Feeds from:** [nmap](01-nmap.md) (`-sV -oX`) · leads into [Metasploit](09-metasploit.md) (`search` often has the same exploit as a module)

← [Back to index](../README.md)
