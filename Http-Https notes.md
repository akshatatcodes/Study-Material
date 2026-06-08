# 🌐 Akshat's Notes — File 1
## HTTP, HTTPS & Web Security Basics

> Simple language. Real examples. Everything you need.
> Day 5 of your 60-Day Cybersecurity Roadmap 🔥

---

# 🎯 Why Web Security?

**Every company has a website. Every website can be attacked.**

As a SOC analyst or pentester, web security is your daily job:
- 43% of all data breaches involve web applications (Verizon DBIR 2023)
- OWASP says web app attacks are the #1 breach vector
- Bug bounty programs pay ₹10,000 to ₹1,00,000+ per vulnerability found

Understanding HTTP and HTTPS is the foundation of ALL web security work.

---

# 📦 PART 1 — HTTP (HyperText Transfer Protocol)

## What Is HTTP? — The Simple Explanation

HTTP is the **language** that your browser and web servers use to talk to each other. When you open a website, your browser sends an HTTP **request**, and the server sends back an HTTP **response**.

**Real Life Example 🌍**
> Imagine ordering food at a restaurant:
> - You (browser) tell the waiter (HTTP): "I want Butter Chicken" → **REQUEST**
> - Waiter goes to kitchen (server) and comes back with food → **RESPONSE**
>
> HTTP is the waiter — the messenger between you and the kitchen.

```
Browser                           Web Server
   │                                   │
   │  ── HTTP Request ──────────────►  │
   │  "GET /index.html HTTP/1.1"       │
   │  Host: google.com                 │
   │                                   │
   │  ◄── HTTP Response ─────────────  │
   │  "HTTP/1.1 200 OK"                │
   │  Content: <html>...</html>        │
   │                                   │
```

---

## HTTP Versions — Know the Differences

| Version | Year | Key Feature | Security |
|---------|------|-------------|---------|
| **HTTP/1.0** | 1996 | One request per connection | No encryption |
| **HTTP/1.1** | 1997 | Keep-alive (reuse connection) | No encryption |
| **HTTP/2** | 2015 | Multiple requests at once (multiplexing) | Usually with TLS |
| **HTTP/3** | 2022 | Uses UDP instead of TCP (QUIC) | Always encrypted |

---

## The HTTP Request — Breaking It Down

When your browser visits a website, it sends a request that looks like this:

```
GET /login.html HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) Chrome/120.0
Accept: text/html,application/xhtml+xml
Accept-Language: en-US,en;q=0.9
Accept-Encoding: gzip, deflate
Cookie: session=abc123xyz; theme=dark
Connection: keep-alive
```

**Every part explained:**

```
GET /login.html HTTP/1.1
│   │           │
│   │           └── HTTP version
│   └────────────── What you want (path/resource)
└────────────────── HTTP Method (what you're doing)

Host: www.example.com         ← Which website (one server can host many sites)
User-Agent: Mozilla/5.0...    ← What browser/tool you're using
Accept: text/html...          ← What type of content you can handle
Cookie: session=abc123        ← Your saved session data (login token)
Connection: keep-alive        ← Don't close connection after this request
```

---

## HTTP Methods — The Verbs

Think of HTTP methods as **actions** you perform on a website.

**Real Life Example 🌍**
> Think of a library:
> - **GET** = Read a book (just look, don't change anything)
> - **POST** = Submit a new book (add something new)
> - **PUT** = Replace an old book with a new edition
> - **DELETE** = Remove a book from the shelf
> - **PATCH** = Fix a typo in one page of a book

| Method | Action | Example | Changes Data? |
|--------|--------|---------|--------------|
| **GET** | Retrieve data | Load a webpage, search | ❌ No |
| **POST** | Send data to server | Login form, post a tweet | ✅ Yes |
| **PUT** | Replace entire resource | Update your whole profile | ✅ Yes |
| **PATCH** | Update part of resource | Change just your phone number | ✅ Yes |
| **DELETE** | Remove resource | Delete a post | ✅ Yes |
| **HEAD** | Get headers only | Check if page exists | ❌ No |
| **OPTIONS** | Ask what's allowed | Browser pre-flight check | ❌ No |

> 🔴 **Security note:** GET requests should NEVER change data. If a website deletes your account via a GET request like `/delete?user=akshat`, that's a **CSRF vulnerability** — any link can trigger it!

---

## HTTP Response — What the Server Sends Back

```
HTTP/1.1 200 OK
Date: Mon, 15 Jan 2024 10:30:00 GMT
Server: Apache/2.4.41 (Ubuntu)
Content-Type: text/html; charset=UTF-8
Content-Length: 1024
Set-Cookie: session=abc123; HttpOnly; Secure
X-Frame-Options: DENY
X-XSS-Protection: 1; mode=block
Strict-Transport-Security: max-age=31536000

<html>
<body>Welcome to Example.com</body>
</html>
```

**The response has 3 parts:**

```
1. STATUS LINE:    HTTP/1.1 200 OK
                   (version, status code, status message)

2. HEADERS:        Date: ..., Server: ..., Content-Type: ...
                   (metadata about the response)

3. BODY:           <html>...</html>
                   (the actual content — webpage, JSON, file, etc.)
```

---

## HTTP Status Codes — The Numbers That Tell You Everything

**Real Life Example 🌍**
> Status codes are like responses from a shopkeeper:
> - 200 = "Here you go!" ✅
> - 301 = "We moved to a new shop at this address" 🔄
> - 403 = "Sorry, members only" 🚫
> - 404 = "We don't have that item" ❓
> - 500 = "Something broke in our storeroom" 💥

### 2xx — Success ✅
| Code | Name | Meaning |
|------|------|---------|
| **200** | OK | Request succeeded. Here's your content. |
| **201** | Created | New resource was created (after POST) |
| **204** | No Content | Success but nothing to return |

### 3xx — Redirect 🔄
| Code | Name | Meaning |
|------|------|---------|
| **301** | Moved Permanently | Page moved forever. Update your bookmarks. |
| **302** | Found (Temp Redirect) | Temporarily at a different URL |
| **304** | Not Modified | Your cached version is still fresh. Use it. |

### 4xx — Client Errors (Your Fault) 🚫
| Code | Name | Meaning | Security Note |
|------|------|---------|--------------|
| **400** | Bad Request | Malformed request | Broken input — could indicate fuzzing |
| **401** | Unauthorized | Not logged in | Must authenticate first |
| **403** | Forbidden | Logged in but no permission | Page EXISTS, you just can't see it |
| **404** | Not Found | Page doesn't exist | Or server is hiding it |
| **405** | Method Not Allowed | That HTTP method isn't accepted here |
| **429** | Too Many Requests | Rate limited — slow down | Triggered by scanning/brute force |

### 5xx — Server Errors (Server's Fault) 💥
| Code | Name | Meaning | Security Note |
|------|------|---------|--------------|
| **500** | Internal Server Error | Server crashed | May reveal stack trace — gold mine for attackers |
| **502** | Bad Gateway | Proxy/load balancer issue |
| **503** | Service Unavailable | Server overloaded | Could be DoS attack |

> 🔴 **Pentesting tip:** The difference between **401** and **403** is HUGE:
> - `401` = You're not authenticated. Log in and try again.
> - `403` = You're authenticated but still can't access it. The page EXISTS — try to bypass it!
>
> During a pentest, 403 pages are worth attacking. 404 means the resource probably doesn't exist.

---

## HTTP Headers — The Hidden Information

Headers carry metadata. As a security person, headers are **intelligence**:

### Request Headers (What YOU Send)
```
User-Agent: Mozilla/5.0...        ← Tells server what browser you use
                                    Attackers change this to hide or impersonate
Referer: https://google.com       ← Where you came from (can leak internal URLs)
Cookie: session=abc123            ← Your session token (steal this = steal account)
Authorization: Bearer eyJhb...    ← API authentication token
X-Forwarded-For: 192.168.1.1     ← Your real IP (if behind proxy)
                                    Attackers fake this to bypass IP restrictions
```

### Response Headers (What SERVER Sends)
```
Server: Apache/2.4.41             ← REVEALS software version!
                                    Attackers check for vulnerabilities in this version
X-Powered-By: PHP/7.4.3           ← REVEALS backend language + version!
Set-Cookie: session=abc; HttpOnly ← Sets your cookie
Content-Type: text/html           ← What type of content this is
Location: /dashboard              ← Redirect destination
```

### Security Response Headers (Good Ones to See)
```
Strict-Transport-Security: max-age=31536000   ← Force HTTPS
X-Frame-Options: DENY                          ← Prevent clickjacking
X-XSS-Protection: 1; mode=block               ← Old XSS protection
Content-Security-Policy: default-src 'self'   ← Modern XSS protection
X-Content-Type-Options: nosniff               ← Prevent MIME sniffing
```

> 💡 **Try it now:** Open terminal and run:
> ```bash
> curl -I https://google.com
> ```
> Look at the Server header — Google hides their server software. Now try:
> ```bash
> curl -I http://testphp.vulnweb.com
> ```
> You'll see `Server: nginx/1.19.0` and `X-Powered-By: PHP/5.6.40` — versions you can search for CVEs!

---

## HTTP Cookies — Your Digital Identity Card

Cookies are small pieces of data that the server stores in your browser to remember you between requests (because HTTP is stateless — it forgets you after every request).

**Real Life Example 🌍**
> Imagine a library that gives you a temporary membership card when you enter. Every time you pick up a book, you show the card so they know who you are without asking your name every time. When you leave, you return the card.
>
> **That card is a cookie.**

```
You visit: example.com
Server says: "Here's your session ID: abc123xyz"
Browser stores cookie: session=abc123xyz

Next page you visit on example.com:
Browser automatically sends: Cookie: session=abc123xyz
Server says: "Oh it's you! You're logged in as akshat."
```

### Cookie Attributes — Security Critical

```
Set-Cookie: session=abc123; HttpOnly; Secure; SameSite=Strict; Path=/; Max-Age=3600
                            │         │       │               │    └── Expires in 1 hour
                            │         │       │               └── Only for / and below
                            │         │       └── Prevents CSRF attacks
                            │         └── Only sent over HTTPS (not HTTP)
                            └── JavaScript CANNOT read this cookie
```

| Attribute | What It Does | Without It |
|-----------|-------------|-----------|
| **HttpOnly** | JavaScript can't read cookie | XSS attack can steal your cookie! |
| **Secure** | Only sent over HTTPS | Cookie sent in plain text over HTTP! |
| **SameSite=Strict** | Not sent with cross-site requests | CSRF attacks possible! |
| **Max-Age/Expires** | When cookie expires | Cookie lasts forever — persistent access |

> 🔴 **Security:** If a cookie is missing `HttpOnly` and there's an XSS vulnerability, an attacker can run `document.cookie` in JavaScript and steal your session. They paste your cookie into their browser and they're logged in as you — without knowing your password!

---

# 🔒 PART 2 — HTTPS (HTTP Secure)

## What Is HTTPS?

HTTPS = HTTP + TLS (Transport Layer Security). Everything is the same as HTTP except all communication is **encrypted**.

**Real Life Example 🌍**
> HTTP is sending a postcard — the postman (anyone on the network) can read everything on it.
>
> HTTPS is sending a letter in a locked box — only you and the recipient have the key. The postman can see WHO you're sending it to (the domain name) but cannot read what's inside.

```
HTTP:   Your data ────────────────────────────────► Server
              ↑
        ANYONE on the network can see:
        "Username: akshat, Password: mypassword123"

HTTPS:  Your data → [ENCRYPT] → ████████████████ → [DECRYPT] → Server
                         ↑
              Network sees only: "Something encrypted going to google.com"
```

---

## TLS Handshake — How HTTPS Starts (Step by Step)

Before any data is exchanged, browser and server go through this process:

```
Step 1 — CLIENT HELLO:
  Browser → Server:
  "Hello! I support TLS 1.3 and TLS 1.2.
   Here are the encryption methods I can use:
   AES-256-GCM, ChaCha20-Poly1305..."

Step 2 — SERVER HELLO:
  Server → Browser:
  "Let's use TLS 1.3 with AES-256-GCM.
   Here is my certificate (signed by DigiCert CA).
   Here's my public key for key exchange."

Step 3 — BROWSER VERIFIES CERTIFICATE:
  Browser checks:
  ✓ Is certificate signed by a trusted CA? (DigiCert is in browser's trust store)
  ✓ Is it expired?
  ✓ Does domain name on cert match the website? (google.com ≠ g00gle.com)
  ✓ Is it revoked?

Step 4 — KEY EXCHANGE:
  Browser and server use the public key to agree on a shared secret key
  WITHOUT ever sending that key over the network
  (Diffie-Hellman key exchange — clever maths!)

Step 5 — ENCRYPTION STARTS:
  Both sides now have the same secret key.
  All further communication is encrypted with it.
  HTTPS is established!
```

---

## SSL Certificates — The Identity Card of a Website

A certificate proves "I really am google.com, not a fake."

```
Certificate contains:
  - Domain name (google.com)
  - Who issued it (Certificate Authority — DigiCert, Let's Encrypt)
  - When it expires
  - The website's public key
  - Digital signature from CA (proves CA verified the identity)
```

### Types of Certificates

| Type | Verification | Shown As | Cost |
|------|-------------|---------|------|
| **DV** (Domain Validation) | Just proves you own the domain | 🔒 Padlock | Free (Let's Encrypt) |
| **OV** (Organisation Validation) | Verifies your company exists | 🔒 Padlock | Paid |
| **EV** (Extended Validation) | Thorough company verification | 🔒 Company name in bar | Expensive |
| **Wildcard** | Covers *.example.com (all subdomains) | 🔒 Padlock | Paid |

> ⚠️ **Important misconception:** HTTPS and the padlock only mean the **connection is encrypted**. They do NOT mean the website is safe or legitimate!
>
> Phishing sites can have HTTPS:
> ```
> https://g00gle.com  ← Has padlock, but it's FAKE!
> ```
> The padlock just means your connection to the fake site is encrypted. The fake site can still steal your password.

---

## HTTP vs HTTPS — Side by Side

| Feature | HTTP | HTTPS |
|---------|------|-------|
| Port | 80 | 443 |
| Encryption | ❌ None | ✅ TLS |
| Passwords visible on network | ✅ Yes | ❌ No |
| Certificate required | ❌ No | ✅ Yes |
| Performance | Slightly faster | Slightly slower (negligible today) |
| SEO (Google ranking) | Penalised | Preferred |
| Browser warning | ⚠️ "Not Secure" | 🔒 Padlock |

---

# 🛡️ PART 3 — WEB SECURITY BASICS

## The OWASP Top 10 — Every Security Person Must Know This

OWASP (Open Web Application Security Project) publishes the top 10 most dangerous web vulnerabilities every few years.

**Think of it as the "Most Wanted" list for web vulnerabilities.**

| # | Vulnerability | Simple Description |
|---|--------------|-------------------|
| A01 | Broken Access Control | Users can do things they shouldn't be able to |
| A02 | Cryptographic Failures | Weak encryption, sensitive data exposed |
| A03 | Injection (SQLi, etc.) | Malicious code injected into queries |
| A04 | Insecure Design | Bad architecture from the start |
| A05 | Security Misconfiguration | Default passwords, debug mode on |
| A06 | Vulnerable Components | Outdated libraries with known bugs |
| A07 | Auth Failures | Weak passwords, session management bugs |
| A08 | Integrity Failures | Insecure deserialization, CI/CD issues |
| A09 | Logging Failures | Attacks not recorded, no monitoring |
| A10 | SSRF | Server makes requests to internal resources |

---

## How a Web Request Works — Full Picture

Let's trace exactly what happens when you type `https://bank.com/login`:

```
1. DNS lookup:
   Your computer: "What is the IP of bank.com?"
   DNS server: "It's 93.184.216.34"

2. TCP connection:
   Your computer → 93.184.216.34:443 (TCP SYN → SYN-ACK → ACK)

3. TLS handshake:
   Certificate exchanged, encryption keys agreed

4. HTTP Request (encrypted):
   GET /login HTTP/1.1
   Host: bank.com
   Cookie: (empty, first visit)

5. HTTP Response (encrypted):
   HTTP/1.1 200 OK
   Set-Cookie: csrf_token=xyz123; SameSite=Strict
   Content: <html>Login form...</html>

6. You fill in form and click Login.
   Browser sends:
   POST /login HTTP/1.1
   Host: bank.com
   Content-Type: application/x-www-form-urlencoded
   
   username=akshat&password=mypassword

7. Server validates credentials.
   Response:
   HTTP/1.1 302 Found
   Location: /dashboard
   Set-Cookie: session=secret_token; HttpOnly; Secure

8. Browser follows redirect to /dashboard.
   Sends Cookie: session=secret_token with every future request.
```

---

## Web Application Architecture — Know the Components

```
Internet
    │
    ▼
[Firewall / WAF]              ← Blocks malicious traffic
    │
    ▼
[Load Balancer]               ← Distributes traffic to multiple servers
    │
    ▼
[Web Server: Nginx/Apache]    ← Handles HTTP, serves static files
    │
    ▼
[Application Server]          ← Runs your code (PHP, Python, Node.js)
    │
    ▼
[Database: MySQL/PostgreSQL]  ← Stores all data
    │
    ▼
[Cache: Redis/Memcached]      ← Speeds up repeated requests
```

Each component can be attacked differently!

---

## Common Web Security Vulnerabilities — Simple Explanations

### 1. Broken Access Control

**What it is:** Users can access things they shouldn't.

**Real Life Example 🌍**
> Imagine a hotel where every room has a lock, but the master key is the same for all rooms and guests can see it. A guest from Room 101 walks into Room 505 because they know the pattern.

```
Normal user visits their profile:
https://example.com/profile?user_id=1001   ← Your profile

They change the number:
https://example.com/profile?user_id=1002   ← Someone else's profile!

If the server shows user 1002's data to user 1001 → BROKEN ACCESS CONTROL (IDOR)
```

This is called **IDOR — Insecure Direct Object Reference**. One of the most common bugs in bug bounty programs.

---

### 2. Security Misconfiguration

**What it is:** Server is set up insecurely — default passwords, debug mode on, unnecessary features enabled.

**Real examples:**
```
Default credentials:
  admin:admin, admin:password, root:root
  Many IoT devices, routers ship with these!

Debug mode enabled in production:
  You visit: https://example.com/crash_me
  Server shows: Full Python stack trace with file paths, database queries,
                environment variables including SECRET_KEY=superSecretKey123

Directory listing enabled:
  You visit: https://example.com/uploads/
  Server shows: Index of /uploads
                backup_2023.zip
                passwords_old.txt
                database_dump.sql     ← Jackpot for attacker!
```

> 💡 **Try it (legally):** Visit `https://httpbin.org/headers` — it shows you exactly what headers your browser is sending. Useful for understanding how requests look.

---

### 3. Sensitive Data Exposure

**What it is:** Sensitive data not properly protected — stored in plain text, transmitted without encryption, backed up carelessly.

```
❌ WRONG — Password stored as plain text in database:
   akshat | password123

❌ WRONG — MD5 hash (crackable in seconds):
   akshat | 482c811da5d5b4bc6d497ffa98491e38

✅ CORRECT — bcrypt hash:
   akshat | $2b$12$rj8MnLcKBxAgL7GUzeUtaOtgFlPQv1YZNKWN/9H3zLQCkX17S3vS.
```

---

### 4. Cross-Site Request Forgery (CSRF)

**What it is:** Attacker tricks your browser into making a request to a website where you're already logged in.

**Real Life Example 🌍**
> You're logged into your bank. You also visit a random website (trap!). That website has a hidden image:
> ```html
> <img src="https://yourbank.com/transfer?to=attacker&amount=50000" width="0" height="0">
> ```
> Your browser automatically loads that "image." Since you're already logged into your bank, your browser sends your session cookie with the request. The bank sees a valid authenticated request and transfers ₹50,000.

**Defence: CSRF Token**
```
Bank adds a random token to every form:
<input type="hidden" name="csrf_token" value="kx9p2mN7qR">

When form is submitted, server checks: is the token valid?
The attacker's fake request won't have the correct token → Rejected!
```

---

### 5. Clickjacking

**What it is:** Attacker puts your website in an invisible iframe on their page. You think you're clicking on their page but you're actually clicking YOUR bank's "Transfer Money" button.

```
Attacker's page (what you see):
  [Win a prize! Click here!]

Attacker's page (what's really there):
  [Win a prize! Click here!]   ← Visible layer (attacker's content)
      [Transfer ₹50,000]       ← Invisible layer (your bank, perfectly aligned!)
```

**Defence: X-Frame-Options header**
```
X-Frame-Options: DENY          ← Can never be in an iframe
X-Frame-Options: SAMEORIGIN   ← Can only be in iframe on same domain
```

---

## Security Headers — The Quick Reference

These headers protect your users. In a security audit, check if they're present.

```bash
# Check all security headers at once:
curl -I https://targetsite.com | grep -i "x-frame\|x-xss\|content-security\|strict-transport\|x-content"
```

| Header | Protects Against | Example Value |
|--------|-----------------|---------------|
| `Strict-Transport-Security` | HTTP downgrade attacks | `max-age=31536000; includeSubDomains` |
| `Content-Security-Policy` | XSS attacks | `default-src 'self'` |
| `X-Frame-Options` | Clickjacking | `DENY` |
| `X-Content-Type-Options` | MIME sniffing | `nosniff` |
| `Referrer-Policy` | Info leakage | `strict-origin-when-cross-origin` |
| `Permissions-Policy` | Feature abuse | `camera=(), microphone=()` |

---

## Burp Suite — Your Web Security Tool

Burp Suite is THE tool for web security testing. It sits between your browser and the server as a **proxy**, letting you see and modify every request.

```
Normal:  Browser ──────────────────────────► Server

With Burp: Browser ──► [Burp Suite] ──────► Server
                               ↑
                         You can see AND modify
                         every request/response here!
```

**Basic workflow:**
```
1. Set browser proxy to 127.0.0.1:8080
2. Burp Suite listens on port 8080
3. Browse normally
4. Every request appears in Burp's "Proxy > HTTP History" tab
5. Right-click any request → "Send to Repeater"
6. Modify and resend the request
7. See the response — look for vulnerabilities
```

---

## How Attackers Enumerate Web Applications

Before attacking, attackers gather information:

```bash
# Find hidden directories and files
dirb http://target.com /usr/share/wordlists/dirb/common.txt
gobuster dir -u http://target.com -w /usr/share/wordlists/dirb/common.txt

# Spider the site (find all pages/links)
# Done through Burp Suite > Target > Site Map

# Check robots.txt (often reveals hidden paths)
curl http://target.com/robots.txt

# Check for common admin pages
curl http://target.com/admin
curl http://target.com/wp-admin
curl http://target.com/phpmyadmin
curl http://target.com/.env      ← Config file with passwords!
curl http://target.com/backup.zip

# Check response headers for technology info
curl -I http://target.com
```

---

## HTTPS Attacks — What Can Go Wrong

Even HTTPS isn't perfect:

| Attack | How It Works | Defence |
|--------|-------------|---------|
| **SSL Stripping** | Attacker downgrades HTTPS to HTTP | HSTS header |
| **Certificate Spoofing** | Fake cert with similar domain name | Certificate Transparency logs |
| **Expired Certificate** | Old cert not renewed — users get warning | Automate cert renewal (Let's Encrypt) |
| **Weak Cipher Suite** | Server accepts old weak encryption | Disable TLS 1.0/1.1, RC4, DES |
| **Heartbleed (CVE-2014-0160)** | Bug in OpenSSL leaked server memory | Patching |

```bash
# Check TLS version and cipher suites
nmap --script ssl-enum-ciphers -p 443 target.com

# Check for common TLS vulnerabilities
sslscan target.com
testssl.sh target.com
```

---

## Practice Right Now

```bash
# 1. See a raw HTTP request
curl -v http://httpbin.org/get 2>&1 | head -50

# 2. See HTTPS in action
curl -v https://httpbin.org/get 2>&1 | head -50
# Notice the TLS handshake in the output!

# 3. Check headers of a website
curl -I https://google.com
curl -I https://facebook.com
# Compare their security headers

# 4. See cookies being set
curl -c /tmp/cookies.txt -v https://httpbin.org/cookies/set/name/akshat
cat /tmp/cookies.txt

# 5. Send a custom User-Agent (like a hacker changing identity)
curl -H "User-Agent: Mozilla/5.0 Googlebot" https://httpbin.org/get

# 6. Make a POST request (like submitting a form)
curl -X POST -d "username=akshat&password=test" https://httpbin.org/post
```

---

# 📌 CHEAT SHEET — Stick This on Your Wall

```
╔══════════════════════════════════════════════════════════╗
║           HTTP / HTTPS QUICK REFERENCE                   ║
╠══════════════════════════════════════════════════════════╣
║  METHODS: GET(read) POST(create) PUT(replace)           ║
║           PATCH(update part) DELETE(remove)              ║
╠══════════════════════════════════════════════════════════╣
║  STATUS CODES:                                           ║
║  200=OK  201=Created  301=Moved  302=Temp Redirect      ║
║  400=Bad Request  401=Not Auth  403=Forbidden           ║
║  404=Not Found  429=Rate Limited  500=Server Error      ║
╠══════════════════════════════════════════════════════════╣
║  COOKIE FLAGS:                                           ║
║  HttpOnly = JS can't read it (blocks XSS theft)         ║
║  Secure   = HTTPS only                                   ║
║  SameSite = Blocks CSRF                                  ║
╠══════════════════════════════════════════════════════════╣
║  SECURITY HEADERS:                                       ║
║  HSTS = Force HTTPS  CSP = Block XSS                   ║
║  X-Frame-Options = Block clickjacking                   ║
╠══════════════════════════════════════════════════════════╣
║  403 ≠ 404: 403 = page EXISTS but blocked (attack it!) ║
║  401 ≠ 403: 401 = not logged in, 403 = no permission   ║
╚══════════════════════════════════════════════════════════╝
```

---

> **Day 5 ✅** — Next: Open Burp Suite, set your browser proxy, visit any website and watch every request appear in real time. That's when HTTP stops being theory and becomes real. 💪
