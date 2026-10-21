# Tutorial: netcat (nc) — the TCP/IP Swiss Army knife

**What it is:** raw TCP/UDP connection tool. No protocol logic, no encryption — just sends and receives bytes. Used for banner grabbing, quick port checks, file transfer, listeners, and catch-a-shell on the exam.

## The five everyday uses

**1. Is this port open? (quick version check)**
```bash
nc -zv target.com 80          # -z = scan, -v = talk about it
nc -zv 10.0.0.5 20-100        # mini port scan
```

**2. Banner grab**
```bash
nc 10.0.0.5 22
# → SSH-2.0-OpenSSH 8.2p1 Ubuntu  ← service + version, no scanner needed
nc 10.0.0.5 80
GET / HTTP/1.0                 # then press Enter twice — raw HTTP by hand
```

**3. Listener / chat between two boxes**
```bash
nc -lvnp 4444                 # box A: listen verbosely on port 4444
nc 10.0.0.99 4444             # box B: connect — now it's a chat
```

**4. File transfer**
```bash
nc -lvnp 4444 > received.zip  # receiver listens and writes to a file
nc 10.0.0.99 4444 < file.zip  # sender pushes the file
```

**5. Catch a reverse shell**
```bash
nc -lvnp 4444                 # on your box — then trigger the payload on the target
# raw nc shell is minimal; upgrade:
python3 -c 'import pty;pty.spawn("/bin/bash")'
```

## Gotchas
- Old/broken `nc` on a system? Try `ncat` or `socat` — same idea, more features.
- `-l` = listen (server side), no `-l` = connect (client side). Getting this backwards is the #1 mistake.
- netcat traffic is **unencrypted** — that's exactly why it shows up in Wireshark questions.

← [Back to index](../README.md)
