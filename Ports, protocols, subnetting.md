# 🛡️ Akshat's Cybersecurity Notes — Day 2
## Ports, Protocols & Subnetting — Complete Deep Dive

> **Simple language. Real-life examples. Everything Professor Messer covered.**
> Day 2 of your 60-Day Cybersecurity Roadmap 🔥

---

## 🗺️ What You'll Learn Today

| Topic | What It Is | Why It Matters |
|-------|-----------|----------------|
| **Ports** | Door numbers for applications | Every attack targets a port. Knowing ports = knowing what's being attacked |
| **Protocols** | Rules for communication | Each protocol has weaknesses attackers exploit |
| **Subnetting** | Dividing networks into smaller pieces | Every network design uses subnetting. Interview favourite topic. |

---

# 🚪 PART 1 — PORTS (Everything You Need to Know)

## What Is a Port? — The Simplest Explanation

Imagine your house. Your **IP address** is your house address. But inside your house, you have multiple **rooms** — living room, kitchen, bedroom, bathroom.

When a delivery person comes to your house (IP address), they need to know **which room** to deliver to. That room number is the **port**.

```
IP Address = Your house address  (192.168.1.100)
Port Number = Which room         (:80, :443, :22)

Full address = 192.168.1.100:443
               ─────────────  ───
               House address   Room number
               (IP)            (Port)
```

### Real Life Example 🌍

> Think of a big hospital. The hospital has ONE address. But inside:
> - Room 101 = Emergency
> - Room 205 = Cardiology
> - Room 310 = Radiology
>
> When an ambulance arrives, it goes to the hospital (IP address) and then straight to Room 101 - Emergency (port 80/443).
>
> **Your computer is the hospital. Different services (web, SSH, email) are different rooms (ports).**

---

## The Three Port Ranges — Must Know

| Range | Name | Who Uses It |
|-------|------|-------------|
| **0 – 1023** | Well-Known Ports | Reserved for standard services. Set by IANA. |
| **1024 – 49151** | Registered Ports | Applications register these (databases, custom apps) |
| **49152 – 65535** | Dynamic / Ephemeral | Randomly assigned to YOUR computer for outbound connections |

### What Are Ephemeral Ports? — Super Important!

When **you** open Chrome and visit a website, your computer needs a port too — not just the server!

```
Your Chrome wants to connect to google.com:

Google's side:  142.250.80.46 : 443  (fixed — always port 443 for HTTPS)
Your side:      192.168.1.5   : 54231 (random ephemeral port chosen by your OS)

The full connection = 192.168.1.5:54231 ←→ 142.250.80.46:443

When you open a second tab → another ephemeral port:
                             192.168.1.5:54232 ←→ 142.250.80.46:443

This is how your computer handles multiple browser tabs simultaneously!
```

> 💡 **Try it:** Open terminal, type `netstat -an` — you'll see YOUR ephemeral ports listed as the source port in every connection your computer has open right now!

---

## Every Port You Must Know — With Real Examples

### 🔴 The Dangerous Ports (Pay Extra Attention)

---

### Port 21/20 — FTP (File Transfer Protocol)

**What it does:** Transfers files between computers.

**Port 21** = Control channel (commands like "list files", "change directory")
**Port 20** = Data channel (actual file transfer)

**Real Life Example 🌍**
> Imagine a post office. Port 21 is the **conversation** at the counter ("I want to send a package to Delhi, it weighs 2kg"). Port 20 is the actual **package being moved** to the truck.

**Why it's dangerous:**
```
When you FTP to a server:
Username: akshat          → Sent in PLAIN TEXT (anyone on network sees this!)
Password: mypassword123   → Sent in PLAIN TEXT (anyone on network sees this!)
File contents             → Sent in PLAIN TEXT

Anyone running Wireshark between you and the server sees EVERYTHING.
```

**FTP Modes (Messer loves asking this):**

| Mode | How It Works | When To Use |
|------|-------------|-------------|
| **Active FTP** | Server connects BACK to the client on port 20. Client must allow inbound connections. | Old networks without NAT |
| **Passive FTP** | Client connects TO the server on a random high port. More firewall-friendly. | Modern networks (most common) |

```
Active FTP:
Client → Server : "I want data, connect to me on port 5000"
Server → Client : connects to client port 5000 (server INITIATES data connection)

Problem: Client's firewall blocks inbound connections from server!

Passive FTP:
Client → Server : "Give me a port to connect to"
Server → Client : "Connect to me on port 50234"
Client → Server : connects to server port 50234 (client INITIATES everything)

Firewall-friendly! Client always initiates.
```

**What to use instead:** **SFTP** (SSH File Transfer Protocol, port 22) — everything encrypted.

---

### Port 22 — SSH (Secure Shell)

**What it does:** Gives you a secure, encrypted terminal on a remote machine. Used by every sysadmin and security person on the planet.

**Real Life Example 🌍**
> Imagine you want to control a computer in a data center in Bangalore from your home in Pune. SSH is like having a **secure, encrypted walkie-talkie** that lets you type commands on that remote computer as if you were sitting in front of it.

```
Without SSH (old way using Telnet):
You type:   "sudo rm -rf /important_folder"
Attacker sees exactly: "sudo rm -rf /important_folder"
(Clear text — readable by anyone intercepting)

With SSH:
You type:   "sudo rm -rf /important_folder"  
Network sees: "4k2!#@jxkq8p2!#kvnx..." (encrypted gibberish)
```

**SSH Authentication methods:**

| Method | How It Works | Security Level |
|--------|-------------|---------------|
| **Password auth** | Type username + password | Moderate — brute-forceable |
| **Key-based auth** | Use a key pair (public + private key) | Very high — practically unbreakable |
| **Certificate auth** | Signed SSH certificates | Highest — enterprise standard |

**SSH Key Pair — Explained Simply:**
```
You generate a key pair:
  Private key → Stays on YOUR computer ONLY. Never share!
  Public key  → Copied to the server

When you connect:
  Server says: "Prove you have the private key matching this public key"
  Your computer: "Here's proof" (cryptographic signature)
  Server: "Verified! Come in."
  
No password ever sent! Even if attacker intercepts, they get nothing useful.
```

**Security Note:** Port 22 is the most brute-forced port on the internet.
- Change SSH to a non-standard port (e.g., 2222) — reduces automated attacks
- Disable password authentication — force key-based only
- Use fail2ban to auto-block IPs after failed attempts

---

### Port 23 — Telnet ☠️

**What it does:** Same as SSH — remote terminal. BUT with ZERO security.

**Real Life Example 🌍**
> Telnet is like having a conversation on a **speakerphone in a public park**. Anyone nearby can hear every word — your username, your password, every command you type, every output you see.

```
Telnet connection captured in Wireshark:
login: admin
Password: admin123
$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
...

EVERYTHING visible to anyone on the same network!
```

**Rule:** If you ever see port 23 open on ANY device in your SOC — that's a **Critical Alert**. Nobody should be using Telnet in 2025. It means:
1. Someone set up an insecure device
2. OR an attacker opened a backdoor

---

### Port 25 — SMTP (Simple Mail Transfer Protocol)

**What it does:** Sends email between mail servers and from your email client to your mail server.

**Real Life Example 🌍**
> SMTP is the postal service. When you send an email, your email app hands it to your mail server (like handing a letter to the post office). Your mail server then uses SMTP to deliver it to the recipient's mail server (like the post office delivers to another city's post office).

```
Email journey using SMTP:

You (Gmail) → Gmail's SMTP server → Recipient's SMTP server → Recipient reads it

Port 25:  Server-to-server email (between mail servers)
Port 587: Client-to-server email with authentication (your app to Gmail)
Port 465: SMTP over SSL (older secure version)
```

**Why attackers love SMTP:**
- Open SMTP relays (misconfigured servers that forward email for anyone) = spam/phishing launching pad
- SMTP can be faked easily (spoofing) — send email that appears to come from boss@company.com

**SPF, DKIM, DMARC — The Three Defenders:**

| Record | Full Name | What It Does | Simple Explanation |
|--------|-----------|-------------|-------------------|
| **SPF** | Sender Policy Framework | Lists which servers can send email for your domain | "Only these specific post offices are allowed to send letters on behalf of my company" |
| **DKIM** | DomainKeys Identified Mail | Adds a digital signature to every email | "Every letter from us has our unique wax seal — fake letters won't have it" |
| **DMARC** | Domain-based Message Authentication | Tells receiving servers what to do with emails that fail SPF/DKIM | "If a letter doesn't have our seal or isn't from our approved post office, reject/quarantine it" |

> 💡 **Try it:** Open terminal and type `nslookup -type=TXT google.com` — you'll see Google's SPF record telling the world which servers can send email as @google.com!

---

### Port 53 — DNS (Domain Name System)

**What it does:** Translates domain names (google.com) to IP addresses (142.250.80.46). The phone book of the internet.

**Uses both TCP and UDP:**
- **UDP port 53:** Normal DNS queries (fast, under 512 bytes)
- **TCP port 53:** Large DNS responses (zone transfers, responses over 512 bytes)

**Real Life Example 🌍**
> Imagine you want to call your friend but you only remember their name, not their number. You call the operator (DNS) and say "I want to call Rahul Sharma in Mumbai." The operator looks up the number and gives it to you. Now you call directly. **DNS is that operator.**

**How DNS Resolution Works — Step by Step:**

```
You type: www.tryhackme.com

Step 1: Check browser cache
        "Did I look this up recently?" → No

Step 2: Check OS cache  
        "Did the system cache this?" → No

Step 3: Ask Recursive Resolver (usually your ISP or 8.8.8.8)
        "What's the IP for www.tryhackme.com?"

Step 4: Recursive Resolver asks Root DNS Server
        "Who handles .com domains?"
        Root says: "Ask the .com TLD nameserver at 192.5.6.30"

Step 5: Recursive Resolver asks .com TLD Server
        "Who handles tryhackme.com?"
        TLD says: "Ask tryhackme's authoritative nameserver at 205.251.196.1"

Step 6: Recursive Resolver asks tryhackme's Authoritative Nameserver
        "What's the IP for www.tryhackme.com?"
        Authoritative says: "104.22.55.228"

Step 7: Recursive Resolver caches the answer and returns to you
        "www.tryhackme.com = 104.22.55.228"
        
Step 8: Your computer connects to 104.22.55.228
        Browser displays TryHackMe!
```

**DNS Record Types — All of Them:**

| Record | Full Name | What It Does | Example |
|--------|-----------|-------------|---------|
| **A** | Address | Maps domain → IPv4 address | google.com → 142.250.80.46 |
| **AAAA** | IPv6 Address | Maps domain → IPv6 address | google.com → 2607:f8b0:4004::200e |
| **CNAME** | Canonical Name | Domain alias → another domain | www.google.com → google.com |
| **MX** | Mail Exchange | Handles email for domain | google.com MX → gmail-smtp-in.l.google.com |
| **TXT** | Text | Stores text (SPF, DKIM, verification) | "v=spf1 include:_spf.google.com ~all" |
| **PTR** | Pointer | IP → domain (reverse lookup) | 142.250.80.46 → google.com |
| **NS** | Nameserver | Authoritative nameserver for domain | google.com NS → ns1.google.com |
| **SOA** | Start of Authority | Info about the DNS zone | Who maintains it, TTL settings |
| **SRV** | Service | Service location for apps | Used by SIP, XMPP |

**Security Attacks on DNS:**

| Attack | How It Works | Defence |
|--------|-------------|---------|
| **DNS Cache Poisoning** | Attacker injects fake DNS records into a resolver's cache. Victims get sent to wrong IP. | DNSSEC (digital signatures on DNS records) |
| **DNS Tunnelling** | Attacker hides data inside DNS queries to exfiltrate from network. Uses subdomain like `data.evil.com`. | Monitor DNS traffic for anomalous query volumes/lengths |
| **DNS Amplification DDoS** | Attacker sends small DNS query with spoofed victim's IP. DNS responds with large answer TO the victim. | Rate limiting, BCP38 filtering |
| **DNS Hijacking** | Attacker compromises DNS settings (on router or registrar) to redirect all traffic | DNSSEC, monitor DNS settings |
| **NXDOMAIN Attack** | Flood DNS server with queries for nonexistent domains | Rate limiting |

> 💡 **Try it:** `nslookup -type=MX gmail.com` — see Gmail's mail servers. `nslookup -type=TXT google.com` — see SPF records!

---

### Port 67/68 — DHCP (Dynamic Host Configuration Protocol)

**What it does:** Automatically assigns IP addresses to devices joining a network.

**Real Life Example 🌍**
> When you join a new hostel, the warden automatically assigns you a room number. You don't pick it — they handle it. That's DHCP. Your phone joins a Wi-Fi network, DHCP gives it an IP address automatically.

**DHCP Process — DORA (Must Memorise!):**

```
D — DISCOVER:  Your device joins network, shouts to everyone:
               "Is there a DHCP server here? I need an IP address!"
               (Sent to broadcast: 255.255.255.255, because you don't have IP yet!)

O — OFFER:     DHCP server hears you and says:
               "Here! I offer you IP 192.168.1.50 for 24 hours"

R — REQUEST:   Your device says:
               "Yes please, I'd like 192.168.1.50!"
               (Broadcast again — in case multiple DHCP servers offered)

A — ACK:       DHCP server confirms:
               "Done! 192.168.1.50 is yours for 24 hours.
                Your gateway is 192.168.1.1
                Your DNS is 8.8.8.8"

Port 67 = DHCP Server port (receives requests)
Port 68 = DHCP Client port (receives offers)
```

**DHCP Lease — What Gets Assigned:**
- IP address (e.g., 192.168.1.50)
- Subnet mask (e.g., 255.255.255.0)
- Default gateway (e.g., 192.168.1.1 — your router)
- DNS server(s) (e.g., 8.8.8.8)
- Lease duration (e.g., 24 hours — after which it must renew)

**Security Attacks on DHCP:**

| Attack | What Happens | Defence |
|--------|-------------|---------|
| **DHCP Starvation** | Attacker floods server with DISCOVER requests using fake MAC addresses. Exhausts IP pool. Legitimate devices can't get IPs. | Port security (limit MACs per port) |
| **Rogue DHCP Server** | Attacker sets up fake DHCP server. Hands out wrong gateway (attacker's device) → MITM attack. | DHCP Snooping (switch only trusts DHCP from specific ports) |

---

### Port 80 — HTTP (HyperText Transfer Protocol)

**What it does:** Transfers web pages. The foundation of the World Wide Web.

**Real Life Example 🌍**
> HTTP is like sending postcards. Your message travels from sender to receiver — but **anyone handling the postcard along the way can read everything on it**. Your message, your name, your address — all visible.

```
You visit: http://example.com/login and type your password

What travels on the network:
POST /login HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded

username=akshat&password=mypassword123

EVERY WORD visible to anyone sniffing traffic!
```

**HTTP Methods — CRUD Operations:**

| Method | What It Does | Real Example |
|--------|-------------|-------------|
| **GET** | Retrieve a resource | Load a web page, load an image |
| **POST** | Send data to server | Submit a login form, post a tweet |
| **PUT** | Replace/update a resource | Update your profile |
| **DELETE** | Remove a resource | Delete a post |
| **HEAD** | Get headers only, no body | Check if page exists before downloading |
| **OPTIONS** | Ask what methods are allowed | Browser CORS preflight request |
| **PATCH** | Partially update a resource | Change only your phone number in profile |

**HTTP Status Codes — Know These:**

| Code | Meaning | When You'll See It |
|------|---------|-------------------|
| **200** | OK | Successful request |
| **301** | Moved Permanently | Site redirects to new URL |
| **302** | Found (Temporary Redirect) | Temporary redirect |
| **400** | Bad Request | Malformed request (broken URL) |
| **401** | Unauthorized | Not logged in |
| **403** | Forbidden | Logged in but no permission |
| **404** | Not Found | Page doesn't exist |
| **500** | Internal Server Error | Server-side bug |
| **503** | Service Unavailable | Server overloaded or down |

> 🔴 **Security note:** 401 vs 403 matters! 401 = "you're not logged in." 403 = "you're logged in but you don't have permission." During a pentest, getting a 403 instead of 404 tells you the page EXISTS — you just can't access it yet.

---

### Port 443 — HTTPS (HTTP Secure)

**What it does:** HTTP but encrypted with TLS. Everything you see in HTTP, but the content is scrambled so only the intended recipient can read it.

**Real Life Example 🌍**
> HTTPS is sending a letter in a locked, tamper-proof box. The courier (network) can see WHO you're sending it to (the domain name), but cannot open the box to see WHAT's inside (your data).

```
With HTTPS:
Network can see: "This connection is going to google.com on port 443"
Network CANNOT see: "GET /search?q=hacking+tutorials HTTP/1.1"
                    "Cookie: sessionid=abc123"
                    "Authorization: Bearer eyJhbGci..."
```

**TLS Handshake — How HTTPS Starts:**

```
Step 1 — CLIENT HELLO:
  Browser → Server: "Hello! I support TLS 1.3, TLS 1.2.
                     Here are the encryption methods I support."

Step 2 — SERVER HELLO:
  Server → Browser: "Great, let's use TLS 1.3 with AES-256.
                     Here's my certificate (signed by a trusted CA)."

Step 3 — CERTIFICATE VERIFICATION:
  Browser: "Is this certificate valid? Signed by a trusted CA? Not expired?
            Does the domain name match?" → Yes → Continue

Step 4 — KEY EXCHANGE:
  Browser and Server use the certificate to securely agree on an
  encryption key (without sending the key itself over the network!)

Step 5 — ENCRYPTED:
  All further communication is encrypted with the agreed key.
```

**What the padlock in your browser means:**
- ✅ Certificate is valid and signed by a trusted CA
- ✅ Domain name matches the certificate
- ✅ Connection is encrypted
- ❌ Does NOT mean the website is safe! (Phishing sites can have HTTPS too!)

---

### Port 110/995 — POP3 (Post Office Protocol)

**What it does:** Downloads email from server to your device. After download, email is usually deleted from server.

**Real Life Example 🌍**
> POP3 is like picking up your physical mail from the post office. Once you take it, it's in your hands — not at the post office anymore. If your phone burns down, the mail is gone.

```
Port 110 = POP3 (no encryption — BAD)
Port 995 = POP3S (POP3 over SSL — use this!)

POP3 behaviour:
- Downloads all email to device
- Deletes from server (usually)
- Can't sync between devices (phone and laptop get different emails)
```

---

### Port 143/993 — IMAP (Internet Message Access Protocol)

**What it does:** Accesses email on the server — email stays on server, device just views it.

**Real Life Example 🌍**
> IMAP is like reading your email through a window at the post office. The letters stay at the post office. You can read them from any window (any device) and they're all the same. Delete on your phone = deleted on your laptop too.

```
Port 143 = IMAP (no encryption — BAD)
Port 993 = IMAPS (IMAP over SSL — use this!)

IMAP behaviour:
- Email stays on server
- All devices sync — same inbox everywhere
- Delete on one device = delete on all
- Most modern email (Gmail, Outlook) uses IMAP
```

**POP3 vs IMAP:**

| Feature | POP3 | IMAP |
|---------|------|------|
| Email stored | On your device | On server |
| Multiple devices | No sync | Fully synced |
| Access without internet | Yes (already downloaded) | No (needs server connection) |
| Server storage | Less (deleted after download) | More (stays forever) |
| Use case | One device, offline access | Multiple devices, modern use |

---

### Port 161/162 — SNMP (Simple Network Management Protocol)

**What it does:** Monitors and manages network devices — routers, switches, servers, printers. Used by network administrators to check device health, read statistics, and configure devices remotely.

**Real Life Example 🌍**
> SNMP is like a hospital's patient monitoring system. Each patient (network device) has sensors (SNMP agent) sending vitals to the central monitor (SNMP manager). The central monitor can see CPU usage, bandwidth, errors — and can even send commands to change settings.

```
Port 161 = SNMP agent (device being monitored receives polls)
Port 162 = SNMP trap (device SENDS alerts to manager when something happens)

SNMP Components:
  Manager = Central system monitoring everything (your network management server)
  Agent   = Software on each device being monitored (router, switch)
  MIB     = Management Information Base = list of things you can monitor
  OID     = Object Identifier = specific thing you want to read (e.g., CPU usage)
```

**SNMP Versions — Security Matters Hugely:**

| Version | Security | Community String | Use? |
|---------|---------|-----------------|------|
| **SNMPv1** | None | Plain text ("public", "private") | NEVER — completely insecure |
| **SNMPv2c** | None | Plain text | NEVER — still insecure |
| **SNMPv3** | Authentication + Encryption | No community string — uses keys | ALWAYS use this |

**Why SNMPv1/v2 is dangerous:**
```
SNMPv1 uses community strings (basically passwords):
  Read community:  "public"  (default — everyone knows this!)
  Write community: "private" (default — everyone knows this!)
  
Attacker sends: SNMP query with community string "public"
Device responds: Here's all my configuration, routing tables, interfaces, passwords!

Attacker can even WRITE with "private" — change device settings remotely!
```

> 💡 **In security audits:** Checking for open port 161 with default community strings ("public"/"private") is one of the first things you do. Shocking how many enterprise devices still have default strings!

---

### Port 389/636 — LDAP (Lightweight Directory Access Protocol)

**What it does:** Accesses directory services — think of Active Directory. Used to look up users, groups, computers in an organisation.

**Real Life Example 🌍**
> LDAP is like the HR department's employee directory. When you log into your company computer, it checks LDAP (Active Directory) to verify your username and password, then looks up what groups you belong to, what permissions you have, etc.

```
Port 389 = LDAP (no encryption — BAD)
Port 636 = LDAPS (LDAP over SSL — use this!)

LDAP is used for:
  - Windows Active Directory authentication
  - Corporate single sign-on (SSO)
  - VPN authentication
  - Any centralised user management
```

**Security:** LDAP without SSL (port 389) sends credentials in plain text. Always use port 636 (LDAPS).

---

### Port 3389 — RDP (Remote Desktop Protocol)

**What it does:** Full graphical remote desktop access to Windows machines. Like sitting in front of the computer — but remotely.

**Real Life Example 🌍**
> RDP is like having a magical screen that shows you exactly what's on a computer in another city. You can see the desktop, move the mouse, type, open programs — everything. Used by IT support to fix computers remotely.

**Why RDP is the #1 brute-force target:**
```
Port 3389 open to internet = Attackers constantly hammering it with passwords

Default accounts attackers try:
  Administrator:password
  Administrator:admin
  Administrator:123456
  Administrator:Password1
  Administrator:(blank)
  
Scripts run 24/7 scanning the entire internet for port 3389.
If found, they try thousands of passwords automatically.
```

**The BlueKeep Vulnerability (CVE-2019-0708):**
- Critical RDP vulnerability discovered in 2019
- Allowed unauthenticated Remote Code Execution
- Affected Windows 7, Server 2008 and older
- Over 900,000 machines were vulnerable when discovered
- NSA issued emergency warning

**Defence:**
- NEVER expose RDP directly to internet
- Use VPN first, then RDP
- Enable Network Level Authentication (NLA)
- Require MFA
- Change default port (minor defence — still vulnerable but reduces automated scanning)
- Keep patched

---

### Port 445 — SMB (Server Message Block)

**What it does:** Windows file and printer sharing. Lets computers share folders and printers on a network.

**Real Life Example 🌍**
> SMB is what lets you see your colleague's shared folder on your office network drive. When you map a network drive (Z:\ drive pointing to \\server\files), that's SMB.

**Why SMB is infamous in security:**

**EternalBlue (MS17-010) — The Most Devastating SMB Exploit:**
```
2017: NSA discovered a critical bug in SMB (port 445)
2017: Shadow Brokers (hacker group) LEAKED the NSA's exploit tool
2017: WannaCry ransomware used EternalBlue to spread
      → Infected 200,000+ computers in 150 countries in 24 hours
      → NHS UK shut down, FedEx paralysed, Telefónica crippled
      
All because port 445 was open and unpatched!
```

**NotPetya, also 2017:** Another ransomware using EternalBlue — caused $10 billion in damage globally.

**Defence:**
- ALWAYS keep Windows patched (MS17-010 patch was available BEFORE WannaCry)
- Block port 445 at your perimeter firewall — no need for external access
- Disable SMBv1 (extremely old, extremely vulnerable)

---

### Port 3306 — MySQL

**What it does:** MySQL database server port. Applications connect to MySQL to store and retrieve data.

**Real Life Example 🌍**
> Your bank's website (PHP/Python app) needs to store your account balance somewhere. It connects to MySQL on port 3306 and says "Get me the balance for account #12345." MySQL returns "₹50,000." The website shows you ₹50,000.

**Why exposing port 3306 to internet is catastrophic:**
```
MySQL has user accounts and passwords.
If attacker reaches port 3306 directly:
  - Brute force MySQL root password
  - If default/weak password exists → Access to ALL databases
  - Read customer data, passwords, credit card numbers
  - DROP TABLE users → destroy all data
  
Databases should ONLY be accessible from the application server.
NEVER from the internet!
```

**Architecture (correct vs wrong):**
```
WRONG (dangerous):
Internet → [MySQL:3306] ← Attacker directly attacks database

CORRECT:
Internet → Web Server → [MySQL:3306] ← Database only reachable from web server
                                        Not from internet!
```

---

## Full Port Reference Table — Stick This On Your Wall

| Port | Service | TCP/UDP | Encrypted? | Risk Level | Quick Note |
|------|---------|---------|-----------|-----------|-----------|
| 20/21 | FTP | TCP | ❌ | 🔴 High | Use SFTP instead |
| 22 | SSH/SFTP | TCP | ✅ | 🟡 Medium | Brute-force target |
| 23 | Telnet | TCP | ❌ | 🔴 Critical | NEVER use |
| 25 | SMTP | TCP | ⚠️ | 🟡 Medium | Check SPF/DKIM/DMARC |
| 53 | DNS | TCP/UDP | ⚠️ | 🟡 Medium | Watch for tunnelling |
| 67/68 | DHCP | UDP | ❌ | 🟡 Medium | DHCP snooping needed |
| 80 | HTTP | TCP | ❌ | 🟡 Medium | Redirect to HTTPS |
| 110 | POP3 | TCP | ❌ | 🔴 High | Use POP3S (995) |
| 123 | NTP | UDP | ❌ | 🟢 Low | Time sync, amplification risk |
| 143 | IMAP | TCP | ❌ | 🔴 High | Use IMAPS (993) |
| 161 | SNMP | UDP | ❌ v1/v2 | 🔴 High | Use SNMPv3 only |
| 162 | SNMP Trap | UDP | ❌ v1/v2 | 🔴 High | Use SNMPv3 only |
| 389 | LDAP | TCP | ❌ | 🔴 High | Use LDAPS (636) |
| 443 | HTTPS | TCP | ✅ | 🟢 Low | Check cert, TLS version |
| 445 | SMB | TCP | ✅ | 🔴 Critical | Block at perimeter, patch! |
| 636 | LDAPS | TCP | ✅ | 🟢 Low | Secure LDAP |
| 993 | IMAPS | TCP | ✅ | 🟢 Low | Secure IMAP |
| 995 | POP3S | TCP | ✅ | 🟢 Low | Secure POP3 |
| 1433 | MSSQL | TCP | ⚠️ | 🔴 High | Never expose to internet |
| 3306 | MySQL | TCP | ⚠️ | 🔴 High | Never expose to internet |
| 3389 | RDP | TCP | ✅ | 🔴 Critical | VPN + MFA mandatory |
| 5432 | PostgreSQL | TCP | ⚠️ | 🔴 High | Never expose to internet |
| 8080 | HTTP-Alt | TCP | ❌ | 🟡 Medium | Web proxies, dev servers |
| 8443 | HTTPS-Alt | TCP | ✅ | 🟢 Low | Alt HTTPS |

---

# 🌐 PART 2 — PROTOCOLS (Deep Dive)

## What Is a Protocol?

A protocol is a **set of rules** that both sides of a communication must follow. Without agreed rules, communication breaks down.

**Real Life Example 🌍**
> When you apply for a job at Deloitte, there's a protocol:
> 1. Submit resume online
> 2. Get email acknowledgement
> 3. Aptitude test
> 4. Technical interview round 1
> 5. Technical interview round 2
> 6. HR round
> 7. Offer letter
>
> Both you AND Deloitte follow this protocol. If you skipped straight to demanding the offer letter, it wouldn't work — the protocol would be broken.
>
> **Network protocols are the same — rules both sides must follow exactly.**

---

## Protocol Deep Dives

### TCP — The Reliable One (Full Deep Dive)

We covered the basics in Day 1. Now let's go deeper.

**TCP Header — What's Inside Every TCP Packet:**

```
TCP Header (20 bytes minimum):

Source Port      (16 bits): Your application's port
Destination Port (16 bits): Application you're talking to
Sequence Number  (32 bits): "This is byte number X in our conversation"
Acknowledgment   (32 bits): "I've received up to byte Y"
Data Offset      (4 bits) : How long the header is
Flags            (9 bits) : SYN, ACK, FIN, RST, PSH, URG, etc.
Window Size      (16 bits): How much data I can accept right now
Checksum         (16 bits): Error detection
Urgent Pointer   (16 bits): Used with URG flag
Options          (variable): Optional extra features
Data             (variable): The actual content
```

**TCP Sequence Numbers — Why They Matter:**

```
Imagine sending a 1000-page textbook:
  You send pages in batches: pages 1-10, 11-20, 21-30...
  If batch 21-30 gets lost, recipient says "I got up to page 20, send 21-30 again!"
  That's TCP sequence numbers.

TCP:
  Sender says: "Here's data, Sequence Number = 100 (starting byte number)"
  Receiver says: "Got it! Acknowledgment = 200 (next byte I expect)"
  Sender sends: Sequence Number = 200 (next batch)
  
If packet lost:
  Receiver says: "ACK = 200 (still waiting for byte 200!)"
  Sender retransmits from byte 200
```

**TCP Window Size — Flow Control:**

```
Problem: Sender might be faster than receiver can process.

Window Size tells sender: "You can send me X bytes before waiting for my ACK"

Example:
  Your phone (receiver) says: "My window = 65535 bytes (I can handle this much)"
  Server sends up to 65535 bytes without waiting for ACK
  When receiver's buffer fills: "My window = 0 (stop sending! I'm busy processing)"
  Server pauses until receiver says "window = 65535 again"

This prevents fast servers from overwhelming slow clients.
```

**TCP Connection States:**

| State | What It Means |
|-------|-------------|
| **LISTEN** | Port is open, waiting for connections |
| **SYN_SENT** | SYN sent, waiting for SYN-ACK |
| **SYN_RECEIVED** | SYN received, SYN-ACK sent, waiting for ACK |
| **ESTABLISHED** | Connection active, data flowing |
| **FIN_WAIT_1** | FIN sent, waiting for ACK |
| **FIN_WAIT_2** | Our FIN acknowledged, waiting for other side's FIN |
| **TIME_WAIT** | Waiting to ensure remote received our final ACK |
| **CLOSE_WAIT** | Remote closed, waiting for local app to close |
| **CLOSED** | Connection fully closed |

> 💡 **Try it:** Run `netstat -an` and look at the STATE column. You'll see ESTABLISHED connections and LISTEN ports right now on your machine!

---

### IP — Internet Protocol (Full Deep Dive)

**IPv4 Header — What's Inside Every Packet:**

```
IPv4 Header (20 bytes minimum):

Version         (4 bits) : Always 4 for IPv4
Header Length   (4 bits) : Length of header in 32-bit words
DSCP/ECN        (8 bits) : Quality of service marking
Total Length    (16 bits): Total packet length (header + data)
Identification  (16 bits): Identifies packet for fragmentation
Flags           (3 bits) : Don't Fragment (DF), More Fragments (MF)
Fragment Offset (13 bits): Position in original packet (for fragmentation)
TTL             (8 bits) : Time To Live — decremented at each router
Protocol        (8 bits) : What's inside (6=TCP, 17=UDP, 1=ICMP)
Header Checksum (16 bits): Error detection for header only
Source IP       (32 bits): Where packet came from
Destination IP  (32 bits): Where packet is going
Data            (variable): The payload (TCP segment, UDP datagram, etc.)
```

**TTL — Time To Live (Super Important for Security):**

```
TTL starts at a value (usually 64, 128, or 255 depending on OS):
  Windows:  TTL starts at 128
  Linux:    TTL starts at 64
  Cisco:    TTL starts at 255

Every router that forwards the packet DECREMENTS TTL by 1.

If TTL reaches 0: Router drops packet + sends ICMP "Time Exceeded" back to sender.

Why TTL exists: Prevents packets from circling the internet forever
               (if routing tables have a loop, TTL kills the packet)
```

**Traceroute uses TTL to map the route!**
```
traceroute google.com:

Sends packet with TTL=1 → First router decrements to 0, sends "Time Exceeded"
  → We learn: Router 1 is at 192.168.1.1 (your home router)

Sends packet with TTL=2 → Second router decrements to 0, sends "Time Exceeded"  
  → We learn: Router 2 is at 49.36.41.1 (your ISP's router)

Sends packet with TTL=3 → Third router...
  → Router 3 is at 72.14.237.13 (ISP backbone)

...continues until packet reaches destination...

Result: Full map of every router between you and Google!
```

**ICMP — Internet Control Message Protocol:**

| ICMP Type | Name | When Sent |
|-----------|------|----------|
| **Type 0** | Echo Reply | Response to ping |
| **Type 3** | Destination Unreachable | Various (port closed, network unreachable) |
| **Type 5** | Redirect | Router tells host to use a better route |
| **Type 8** | Echo Request | Ping! |
| **Type 11** | Time Exceeded | TTL reached 0 (used by traceroute) |
| **Type 13** | Timestamp Request | Time synchronisation |

**ICMP Type 3 Codes (Destination Unreachable):**

| Code | Meaning | What It Tells You |
|------|---------|------------------|
| 0 | Network Unreachable | No route to network exists |
| 1 | Host Unreachable | Network reachable but host down |
| 2 | Protocol Unreachable | Protocol not supported |
| **3** | **Port Unreachable** | **Port is CLOSED (UDP). Sent by target.** |
| 4 | Fragmentation Needed | MTU too small, DF bit set |
| 13 | Communication Administratively Prohibited | Firewall blocked it |

> 🔴 **Security note:** ICMP Type 3 Code 3 (Port Unreachable) is how UDP port scanning detects closed ports! If you send UDP to a closed port, the target sends back ICMP Type 3 Code 3. No response = port open (or filtered).

---

---

# 🔢 PART 3 — SUBNETTING (Complete Deep Dive)

## Why Does Subnetting Exist? — The Real Reason

Imagine a company has 1000 employees. If all 1000 are on ONE big network:
- When anyone broadcasts (ARP, DHCP), ALL 1000 devices receive it → chaos
- One infected computer can directly attack every other computer
- Can't control which department can talk to which
- One failure can affect everyone

**Subnetting fixes this** by dividing the big network into smaller, isolated subnetworks.

**Real Life Example 🌍**
> A big apartment complex has 500 flats. Instead of one massive open building where everyone can walk into anyone's flat, it's divided into **blocks**:
> - Block A: Flats 1-50 (HR Department)
> - Block B: Flats 51-100 (Engineering)
> - Block C: Flats 101-150 (Finance)
>
> Each block has security. To go from Block A to Block B, you go through the main gate (router). You can't just walk between blocks freely.
>
> **That's subnetting.** Each block = a subnet.

---

## Binary — You Must Understand This First!

I know, I know — "Binary?! That sounds hard." But I promise it's just a different way of counting. Give me 5 minutes and you'll get it.

**How we normally count (Decimal — Base 10):**
We use 10 digits: 0, 1, 2, 3, 4, 5, 6, 7, 8, 9
When we run out of digits, we carry to next column: 9 → 10 (tens column starts)

**How computers count (Binary — Base 2):**
Only 2 digits: 0 and 1
When we run out of digits, we carry to next column: 1 → 10 (twos column starts)

```
Decimal:  0  1  2  3  4  5  6  7  8  9  10  11  12  13  14  15
Binary:   0  1 10 11 100 101 110 111 1000 1001 1010 1011 1100 1101 1110 1111
```

**Binary to Decimal — The Easy Way:**

Each bit position has a **value**. From right to left: 1, 2, 4, 8, 16, 32, 64, 128

```
8 bit positions (one octet of an IP address):

Position: 128  64  32  16   8   4   2   1
          ─────────────────────────────────
Bit:        0   0   0   0   0   0   0   0  = 0
Bit:        0   0   0   0   0   0   0   1  = 1
Bit:        0   0   0   0   0   0   1   0  = 2
Bit:        0   0   0   0   0   0   1   1  = 3 (2+1)
Bit:        0   0   0   0   0   1   0   0  = 4
Bit:        1   0   0   0   0   0   0   0  = 128
Bit:        1   1   0   0   0   0   0   0  = 192 (128+64)
Bit:        1   1   1   1   1   1   1   1  = 255 (all bits on)
```

**Practice: Convert 192.168.1.100 to binary:**

```
192 = 128+64 = 11000000
168 = 128+32+8 = 10101000
1   = 00000001
100 = 64+32+4 = 01100100

192.168.1.100 in binary:
11000000.10101000.00000001.01100100
```

**The Key 8-Bit Values to Memorise:**
```
10000000 = 128
11000000 = 192 (128+64)
11100000 = 224 (128+64+32)
11110000 = 240 (128+64+32+16)
11111000 = 248 (128+64+32+16+8)
11111100 = 252 (128+64+32+16+8+4)
11111110 = 254 (128+64+32+16+8+4+2)
11111111 = 255 (all bits = 128+64+32+16+8+4+2+1)
```

---

## Subnet Masks — The Foundation of Everything

### What Is a Subnet Mask?

A subnet mask is a 32-bit number (like an IP address) that tells you:
- Which part of an IP address is the **NETWORK** (which neighbourhood you're in)
- Which part is the **HOST** (which house in that neighbourhood)

**Rules:**
- Network bits are always **1s** (consecutive, from the left)
- Host bits are always **0s** (the remaining bits, to the right)

```
Subnet mask 255.255.255.0 in binary:
11111111.11111111.11111111.00000000
────────────────────────── ────────
    Network bits (24 ones)  Host bits (8 zeros)

This means: First 24 bits = network, Last 8 bits = host
```

**Real Life Example 🌍**
> Think of a phone number: +91-022-2345-6789
>
> +91 = Country (like Class A network — the biggest grouping)
> 022 = City/area code (like the second octet)
> 2345 = Exchange (like the third octet)
> 6789 = Individual line (like the host — specific device)
>
> The subnet mask tells you "how many digits" are the area (network) and how many are the individual line (host).

---

## CIDR Notation — The Shortcut

Writing `255.255.255.0` every time is annoying. CIDR (Classless Inter-Domain Routing) notation is a shortcut — just count how many **1 bits** are in the mask.

```
255.255.255.0 = 11111111.11111111.11111111.00000000 = 24 ones → /24
255.255.0.0   = 11111111.11111111.00000000.00000000 = 16 ones → /16
255.0.0.0     = 11111111.00000000.00000000.00000000 = 8 ones  → /8
255.255.255.128 = 11111111.11111111.11111111.10000000 = 25 ones → /25
```

---

## The Magic Subnet Table — Memorise This!

This table is the key to solving ANY subnetting question in seconds.

| CIDR | Subnet Mask | # Subnets (from /24) | # Hosts | Block Size |
|------|------------|----------------------|---------|------------|
| /24 | 255.255.255.0 | 1 | 254 | 256 |
| /25 | 255.255.255.128 | 2 | 126 | 128 |
| /26 | 255.255.255.192 | 4 | 62 | 64 |
| /27 | 255.255.255.224 | 8 | 30 | 32 |
| /28 | 255.255.255.240 | 16 | 14 | 16 |
| /29 | 255.255.255.248 | 8 hosts/subnet | 6 | 8 |
| /30 | 255.255.255.252 | Point-to-point | 2 | 4 |
| /31 | 255.255.255.254 | Point-to-point | 0+2 | 2 |
| /32 | 255.255.255.255 | Single host | 1 | 1 |

**The formulas:**
```
Number of hosts per subnet = 2^(host bits) - 2

Why subtract 2?
  First IP = Network Address (all host bits = 0) → Cannot be assigned to device
  Last IP  = Broadcast Address (all host bits = 1) → Cannot be assigned to device
  
Example /26:
  Host bits = 32 - 26 = 6 bits
  Total addresses = 2^6 = 64
  Usable hosts = 64 - 2 = 62
```

---

## Subnetting Step-by-Step — Professor Messer's Method

### The 7-Step Method

Professor Messer teaches a systematic approach. Learn this and you can solve ANY subnetting problem:

**Step 1:** Convert mask to CIDR (or vice versa)
**Step 2:** Find host bits = 32 - CIDR number
**Step 3:** Hosts per subnet = 2^(host bits) - 2
**Step 4:** Block size = 2^(host bits)
**Step 5:** Find subnet increments (subnets start at 0, then every block size)
**Step 6:** Find network address (first IP in subnet)
**Step 7:** Find broadcast address (last IP in subnet)

---

### WORKED EXAMPLE 1 — Basic /24

**Question:** You have IP address `192.168.1.50/24`. Find:
- Subnet mask
- Network address
- Broadcast address
- Usable hosts
- Range of usable IPs

**Solution:**

```
Step 1: CIDR = /24
        Mask = 255.255.255.0

Step 2: Host bits = 32 - 24 = 8 bits

Step 3: Hosts per subnet = 2^8 - 2 = 256 - 2 = 254 hosts

Step 4: Block size = 2^8 = 256

Step 5: Subnet starts at .0 (next would be .256 = next network)

Step 6: Network address = 192.168.1.0
        (host portion = all zeros: 192.168.1.00000000)

Step 7: Broadcast = 192.168.1.255
        (host portion = all ones: 192.168.1.11111111)

Usable range: 192.168.1.1 to 192.168.1.254
              (everything between network and broadcast)

ANSWER:
  Subnet Mask:      255.255.255.0
  Network Address:  192.168.1.0
  Broadcast:        192.168.1.255
  Usable range:     192.168.1.1 – 192.168.1.254
  Number of hosts:  254
```

---

### WORKED EXAMPLE 2 — /26 (This is Where It Gets Interesting!)

**Question:** You have IP address `192.168.1.130/26`. Find network address, broadcast, range.

**Solution:**

```
Step 1: CIDR = /26
        Mask = 255.255.255.192
        (11111111.11111111.11111111.11000000 = 26 ones)

Step 2: Host bits = 32 - 26 = 6 bits

Step 3: Hosts per subnet = 2^6 - 2 = 64 - 2 = 62 hosts

Step 4: Block size = 2^6 = 64

Step 5: Subnets are:
        192.168.1.0   (starts at 0)
        192.168.1.64  (0 + 64)
        192.168.1.128 (64 + 64) ← our IP 130 is in THIS subnet!
        192.168.1.192 (128 + 64)

Step 6: Network address = 192.168.1.128
        (the subnet our IP falls into)

Step 7: Broadcast = 192.168.1.191
        (192.168.1.128 + 64 - 1 = 191)
        (OR: all host bits = 1: 192.168.1.10111111 = 191)

ANSWER:
  Our IP:           192.168.1.130
  Subnet:           192.168.1.128/26
  Network Address:  192.168.1.128
  Broadcast:        192.168.1.191
  Usable range:     192.168.1.129 – 192.168.1.190
  Number of hosts:  62
```

**Visualised:**
```
192.168.1.0   → 192.168.1.63    [Subnet 1: .0/26]
192.168.1.64  → 192.168.1.127   [Subnet 2: .64/26]
192.168.1.128 → 192.168.1.191   [Subnet 3: .128/26] ← 192.168.1.130 is HERE
192.168.1.192 → 192.168.1.255   [Subnet 4: .192/26]
               ▲               ▲
           Network          Broadcast
           .128              .191
```

---

### WORKED EXAMPLE 3 — /27

**Question:** IP address `10.0.0.75/27`. Find all subnet info.

**Solution:**

```
Step 1: CIDR = /27, Mask = 255.255.255.224

Step 2: Host bits = 32 - 27 = 5

Step 3: Hosts = 2^5 - 2 = 32 - 2 = 30

Step 4: Block size = 32

Step 5: Subnets (counting by 32):
        10.0.0.0   ← 0
        10.0.0.32  ← 32
        10.0.0.64  ← 64  ← our IP 75 falls here! (64 ≤ 75 < 96)
        10.0.0.96  ← 96
        10.0.0.128 ← 128
        (continues...)

Step 6: Network = 10.0.0.64

Step 7: Broadcast = 10.0.0.95 (64 + 32 - 1 = 95)

ANSWER:
  Network Address: 10.0.0.64
  Broadcast:       10.0.0.95
  Usable range:    10.0.0.65 – 10.0.0.94
  Hosts:           30
```

---

### WORKED EXAMPLE 4 — Crossing Octets (/20)

**Question:** IP address `172.16.100.1/20`. Find all subnet info.

This one crosses into the THIRD octet. Don't panic — same method!

```
Step 1: CIDR = /20, Mask = 255.255.240.0
        Binary: 11111111.11111111.11110000.00000000
                                  ^^^^────
                              4 bits network  4 bits become host in 3rd octet

Step 2: Host bits = 32 - 20 = 12 bits

Step 3: Hosts = 2^12 - 2 = 4096 - 2 = 4094 hosts

Step 4: Block size = 2^12 = 4096
        But this crosses octets! Block size in the THIRD octet:
        4 host bits in third octet → 2^4 = 16 → block size = 16 in third octet

Step 5: Subnets in third octet (counting by 16):
        172.16.0.x
        172.16.16.x
        172.16.32.x
        172.16.48.x
        172.16.64.x
        172.16.80.x
        172.16.96.x
        172.16.112.x  ← our IP 172.16.100.1 falls here? NO!
        Let me check: 96 ≤ 100 < 112 → YES! 172.16.96.x subnet

Step 6: Network = 172.16.96.0

Step 7: Broadcast = 172.16.111.255 (96+16-1 = 111 in third octet, all 1s in fourth)

ANSWER:
  Network Address: 172.16.96.0
  Broadcast:       172.16.111.255
  Usable range:    172.16.96.1 – 172.16.111.254
  Hosts:           4094
```

---

### WORKED EXAMPLE 5 — /30 (Point-to-Point Links)

**Question:** Why is /30 used for WAN links between two routers?

```
/30 gives:
  Host bits = 32 - 30 = 2 bits
  Hosts = 2^2 - 2 = 4 - 2 = 2 hosts
  
Perfect! Two routers need exactly 2 IP addresses to talk to each other.

Example:
  Network:   10.0.0.0/30
  Router A:  10.0.0.1
  Router B:  10.0.0.2
  Broadcast: 10.0.0.3
  
No wasted IPs. Efficient.
```

---

## Subnetting a Network — Designing from Scratch

This is what real network engineers do. You're given a network and told how many subnets/hosts you need.

### Scenario (Classic Interview Question):

**You are given `192.168.10.0/24`. Your company has 4 departments:**
- Sales: 50 hosts
- Engineering: 100 hosts
- HR: 25 hosts
- Management: 10 hosts

**Design a subnet for each department.**

**Step 1: Find the right subnet size for each department**

```
Remember: You need 2^n - 2 ≥ required hosts
          and n = host bits
          So: find smallest n where 2^n - 2 ≥ required hosts

Engineering: 100 hosts needed
  /25 → 2^7 - 2 = 126 hosts ✅ (126 ≥ 100) — use /25

Sales: 50 hosts needed
  /26 → 2^6 - 2 = 62 hosts ✅ (62 ≥ 50) — use /26

HR: 25 hosts needed
  /27 → 2^5 - 2 = 30 hosts ✅ (30 ≥ 25) — use /27

Management: 10 hosts needed
  /28 → 2^4 - 2 = 14 hosts ✅ (14 ≥ 10) — use /28
```

**Step 2: Assign subnets (largest first — VLSM method)**

```
Start with 192.168.10.0/24 (pool of 256 addresses)

Engineering /25 (needs 128 addresses):
  Subnet: 192.168.10.0/25
  Range:  192.168.10.1 – 192.168.10.126
  Broadcast: 192.168.10.127
  Remaining pool: 192.168.10.128 – 192.168.10.255

Sales /26 (needs 64 addresses):
  Subnet: 192.168.10.128/26
  Range:  192.168.10.129 – 192.168.10.190
  Broadcast: 192.168.10.191
  Remaining pool: 192.168.10.192 – 192.168.10.255

HR /27 (needs 32 addresses):
  Subnet: 192.168.10.192/27
  Range:  192.168.10.193 – 192.168.10.222
  Broadcast: 192.168.10.223
  Remaining pool: 192.168.10.224 – 192.168.10.255

Management /28 (needs 16 addresses):
  Subnet: 192.168.10.224/28
  Range:  192.168.10.225 – 192.168.10.238
  Broadcast: 192.168.10.239
  Remaining pool: 192.168.10.240 – 192.168.10.255 (for future use)
```

**Final Design:**

| Department | Subnet | Range | Broadcast | Capacity |
|------------|--------|-------|-----------|---------|
| Engineering | 192.168.10.0/25 | .1 – .126 | .127 | 126 hosts |
| Sales | 192.168.10.128/26 | .129 – .190 | .191 | 62 hosts |
| HR | 192.168.10.192/27 | .193 – .222 | .223 | 30 hosts |
| Management | 192.168.10.224/28 | .225 – .238 | .239 | 14 hosts |

This technique is called **VLSM — Variable Length Subnet Masking**. Messer loves this!

---

## Special Subnets You Must Know

### /32 — Single Host Route

```
10.0.0.5/32

This is a route to exactly ONE specific IP address.
Network address = 10.0.0.5
Broadcast = 10.0.0.5 (same address!)
Hosts = 1

Used for:
  - Loopback interfaces (127.0.0.1/32)
  - Host-based firewall rules ("only allow this one IP")
  - Advertising a specific host route in routing protocols
```

### /31 — Point-to-Point (RFC 3021)

```
10.0.0.0/31

Technically only 2 addresses, no network/broadcast:
  10.0.0.0 and 10.0.0.1

Modern routers support this for point-to-point links.
Saves one IP compared to /30.
Used in service provider networks.
```

### /0 — Default Route

```
0.0.0.0/0

Matches EVERYTHING. 
The "last resort" route in routing tables.
"If you don't know where to send it, send it here (default gateway)"
```

---

## Supernetting (Route Summarisation) — The Opposite of Subnetting

While subnetting DIVIDES networks, **supernetting COMBINES** multiple networks into a summary route. Used to reduce the size of routing tables.

**Example:**
```
You have 4 networks:
  192.168.0.0/24
  192.168.1.0/24
  192.168.2.0/24
  192.168.3.0/24

Instead of advertising 4 routes, summarise as:
  192.168.0.0/22  (covers all 4 networks!)

Proof:
  192.168.0.0/22 = 192.168.00000000.00000000 to 192.168.00000011.11111111
                 = 192.168.0.0 to 192.168.3.255 ✅ covers all 4!
```

**How to find the summary route:**

```
Step 1: Convert to binary, find the common bits

192.168.0.0:   11000000.10101000.00000000.00000000
192.168.1.0:   11000000.10101000.00000001.00000000
192.168.2.0:   11000000.10101000.00000010.00000000
192.168.3.0:   11000000.10101000.00000011.00000000
                                         ────
Common bits:   11000000.10101000.000000   ← 22 bits match
                                       
Summary: 192.168.0.0/22
```

---

## IPv6 Addressing — The Future

IPv4 is running out (already exhausted!). IPv6 was created to solve this.

**IPv4:** 32 bits → ~4.3 billion addresses → EXHAUSTED
**IPv6:** 128 bits → 340 undecillion addresses → Will never run out

**340 undecillion = 340,000,000,000,000,000,000,000,000,000,000,000,000**
(That's more than the number of atoms on Earth!)

### IPv6 Address Format

```
IPv6 uses 128 bits, written as 8 groups of 4 hex digits:

2001:0db8:85a3:0000:0000:8a2e:0370:7334
────  ────  ────  ────  ────  ────  ────  ────
 1    2     3     4     5     6     7     8
(Each group = 16 bits = 4 hex digits)
```

### IPv6 Shortening Rules

**Rule 1:** Remove leading zeros in each group
```
2001:0db8:0000:0000:0000:0000:0000:0001
→    2001:db8:0:0:0:0:0:1
```

**Rule 2:** Replace ONE consecutive run of all-zero groups with `::`
```
2001:db8:0:0:0:0:0:1
→    2001:db8::1
(The :: replaces all those zero groups)
```

**Important:** `::` can only be used ONCE per address (otherwise ambiguous!)

### Special IPv6 Addresses

| Address | Equivalent | Meaning |
|---------|-----------|---------|
| `::1` | `0:0:0:0:0:0:0:1` | Loopback (like 127.0.0.1) |
| `::` | All zeros | Unspecified address |
| `fe80::/10` | fe80 to febf | Link-local (auto-configured, not routable) |
| `ff00::/8` | ff + rest | Multicast addresses |
| `2000::/3` | 2000 to 3fff | Global unicast (internet-routable) |
| `fc00::/7` | fc-fd | Unique local (like private IPv4 — RFC 1918) |

### IPv6 vs IPv4 — Key Differences

| Feature | IPv4 | IPv6 |
|---------|------|------|
| Address length | 32 bits | 128 bits |
| Address format | Decimal (192.168.1.1) | Hexadecimal (2001:db8::1) |
| Total addresses | ~4.3 billion | 340 undecillion |
| NAT required? | Yes (due to shortage) | No (enough addresses) |
| Broadcast | Yes (255.255.255.255) | No — replaced by multicast |
| Header complexity | Variable, complex | Fixed 40 bytes, simpler |
| Auto-configuration | DHCP needed | SLAAC (self-configuring!) |
| ARP | Yes | Replaced by NDP (Neighbor Discovery Protocol) |
| IPSec | Optional | Built-in (mandatory in original spec) |

### IPv6 Subnetting

IPv6 uses /64 for standard subnets. The first 64 bits = network, last 64 bits = host (EUI-64 or random).

```
A /48 allocation (typical for an organisation):

ISP gives you: 2001:db8:1234::/48
You have 16 bits left for subnetting (48 to 64)
That's 2^16 = 65,536 possible /64 subnets!

Example subnets:
  2001:db8:1234:0000::/64  ← VLAN 1
  2001:db8:1234:0001::/64  ← VLAN 2
  2001:db8:1234:0002::/64  ← VLAN 3
  ...up to...
  2001:db8:1234:ffff::/64  ← VLAN 65536
```

---

## NAT — Network Address Translation (Deep Dive)

We mentioned NAT earlier — let's go deep.

### Why NAT Exists

IPv4 addresses ran out. Billions of devices need internet access but there aren't enough public IPs. NAT is the bandage solution — let many private IPs share one public IP.

**Real Life Example 🌍**
> Your office building has ONE phone number (the main reception number). Inside, there are 500 extension numbers. When someone calls from outside, they call the main number (public IP). The receptionist (NAT router) routes the call to the right extension (private IP). When an employee calls out, the call appears to come from the main number — no one knows which extension made the call.

### Three Types of NAT

**1. Static NAT — One-to-One Mapping**
```
Always:
  Private IP 192.168.1.10 ←→ Public IP 203.0.113.10 (fixed mapping)

Use case: Your web server needs a permanent public IP
          External users always reach 203.0.113.10 → NAT → 192.168.1.10
```

**2. Dynamic NAT — Pool Mapping**
```
Pool of public IPs: 203.0.113.10 to 203.0.113.20 (10 IPs)
Internal network: 192.168.1.0/24 (254 devices)

When 192.168.1.50 wants internet:
  NAT assigns available pool IP (say 203.0.113.15)
  192.168.1.50 ←→ 203.0.113.15 (while session active)
  
When session ends, 203.0.113.15 goes back to pool

Problem: If all 10 pool IPs are in use, the 11th user can't get internet!
```

**3. PAT (Port Address Translation) — Many-to-One**
```
ONE public IP: 203.0.113.10
Many internal devices

Device 192.168.1.10 opens Chrome → NAT: 203.0.113.10:5000 (random port)
Device 192.168.1.11 opens Chrome → NAT: 203.0.113.10:5001
Device 192.168.1.12 opens Chrome → NAT: 203.0.113.10:5002

When response comes back to 203.0.113.10:5000 → NAT knows → forward to 192.168.1.10
When response comes back to 203.0.113.10:5001 → NAT knows → forward to 192.168.1.11

This is what your home router does!
65,535 ports available → can handle MANY simultaneous connections on ONE public IP
```

### NAT Table — What the Router Tracks

```
Inside Local IP:Port    Inside Global IP:Port    Outside IP:Port
──────────────────────  ─────────────────────    ────────────────
192.168.1.10:54231    203.0.113.10:5000       142.250.80.46:443
192.168.1.11:49876    203.0.113.10:5001       142.250.80.46:443
192.168.1.12:50001    203.0.113.10:5002       8.8.8.8:53
192.168.1.10:54232    203.0.113.10:5003       172.217.0.1:80

When packet comes in for 203.0.113.10:5001 → NAT looks up table → forwards to 192.168.1.11:49876
```

---

## APIPA — Automatic Private IP Addressing

When your device can't find a DHCP server, it auto-assigns itself an IP from:
**169.254.0.0/16** (169.254.0.1 to 169.254.255.254)

**When you see this:**
```
Your device shows IP: 169.254.x.x

This means: DHCP server is unreachable!
  Possible causes:
  - DHCP server is down
  - Network cable unplugged
  - Wi-Fi not connected properly
  - DHCP server ran out of IPs (pool exhausted)
  
Devices with APIPA IPs can only communicate with other APIPA devices
on the same local segment. NO internet access!
```

---

## Private IP Ranges (RFC 1918) — Complete Guide

These ranges are reserved for private networks. Routers do NOT forward these to the internet.

```
Class A Private: 10.0.0.0 – 10.255.255.255   (/8)
  Total IPs: 16,777,216
  Used by:  Large enterprises, ISP internal networks, cloud providers

Class B Private: 172.16.0.0 – 172.31.255.255  (/12)
  Total IPs: 1,048,576
  Used by:  Medium enterprises, Docker default network (172.17.0.0/16)

Class C Private: 192.168.0.0 – 192.168.255.255 (/16)
  Total IPs: 65,536
  Used by:  Home networks, small offices (your home router uses this!)
  
Loopback:        127.0.0.0 – 127.255.255.255   (/8)
  127.0.0.1 = localhost = your own machine
  
Link-Local:      169.254.0.0 – 169.254.255.255  (/16)
  APIPA addresses — no DHCP available
```

---

# 🧮 PART 4 — SUBNETTING CHEAT SHEET & QUICK TRICKS

## The "Magic Number" Trick

For ANY subnet, the magic number = **256 - subnet mask value in the interesting octet**

```
Example: /26 = 255.255.255.192
Interesting octet = 4th octet (where the masking is happening)
Magic number = 256 - 192 = 64

Subnets start at: 0, 64, 128, 192
(just count by 64!)
```

```
Example: /27 = 255.255.255.224
Magic number = 256 - 224 = 32
Subnets: 0, 32, 64, 96, 128, 160, 192, 224
```

```
Example: /20 = 255.255.240.0
Interesting octet = 3rd octet (240 is not 0 or 255)
Magic number = 256 - 240 = 16
Subnets in 3rd octet: 0, 16, 32, 48, 64, 80, 96, 112, 128, 144, 160, 176, 192, 208, 224, 240
```

## Complete CIDR Reference Table

| CIDR | Mask | Hosts | Block | Subnets in /24 |
|------|------|-------|-------|----------------|
| /1 | 128.0.0.0 | 2,147,483,646 | 2,147,483,648 | - |
| /8 | 255.0.0.0 | 16,777,214 | 16,777,216 | - |
| /16 | 255.255.0.0 | 65,534 | 65,536 | - |
| /17 | 255.255.128.0 | 32,766 | 32,768 | - |
| /18 | 255.255.192.0 | 16,382 | 16,384 | - |
| /19 | 255.255.224.0 | 8,190 | 8,192 | - |
| /20 | 255.255.240.0 | 4,094 | 4,096 | - |
| /21 | 255.255.248.0 | 2,046 | 2,048 | - |
| /22 | 255.255.252.0 | 1,022 | 1,024 | - |
| /23 | 255.255.254.0 | 510 | 512 | - |
| **/24** | **255.255.255.0** | **254** | **256** | **1** |
| /25 | 255.255.255.128 | 126 | 128 | 2 |
| /26 | 255.255.255.192 | 62 | 64 | 4 |
| /27 | 255.255.255.224 | 30 | 32 | 8 |
| /28 | 255.255.255.240 | 14 | 16 | 16 |
| /29 | 255.255.255.248 | 6 | 8 | 32 |
| /30 | 255.255.255.252 | 2 | 4 | 64 |
| /31 | 255.255.255.254 | 0 (or 2) | 2 | 128 |
| /32 | 255.255.255.255 | 1 | 1 | 256 |

---

# ❓ PART 5 — INTERVIEW QUESTIONS ON PORTS, PROTOCOLS & SUBNETTING

---

**Q1: What is the difference between port 110 and port 143?**

> "Both are email reading protocols. Port 110 is POP3 — it downloads email to your device and removes it from the server. Port 143 is IMAP — email stays on the server and all your devices stay in sync. In security, both of these unencrypted versions (110 and 143) are dangerous because credentials and emails travel in plain text. Always use POP3S (995) and IMAPS (993) instead."

---

**Q2: Why is Telnet (port 23) dangerous?**

> "Telnet sends everything in plain text — your username, password, and every command you type. Anyone on the same network running a packet sniffer like Wireshark can see all of this instantly. SSH (port 22) does exactly the same thing as Telnet — remote shell access — but everything is encrypted. There is no reason to ever use Telnet on a modern network. If I see port 23 open in a SOC, it's an immediate investigation."

---

**Q3: What is a SYN flood and how would you defend against it?**

> "A SYN flood is a Layer 4 DoS attack. The attacker sends thousands of TCP SYN packets — the first step of the 3-way handshake — but never sends the final ACK. The server allocates resources for each half-open connection and fills its connection table. Legitimate users can't connect. Defence: SYN cookies — the server doesn't allocate memory until it receives the final ACK. Rate limiting also helps — drop SYN packets above a threshold. Upstream scrubbing services (like Cloudflare) absorb the flood before it reaches your server."

---

**Q4: An IP is 192.168.1.100/26. What subnet is it in?**

> "/26 gives a block size of 64. Subnets are at .0, .64, .128, .192. The IP .100 falls between .64 and .128, so it's in the .64 subnet: 192.168.1.64/26. Network address is 192.168.1.64, broadcast is 192.168.1.127, usable range is .65 to .126, with 62 usable hosts."

---

**Q5: What is VLSM and why is it better than fixed subnetting?**

> "VLSM — Variable Length Subnet Masking — allows you to use different subnet sizes within the same network. Traditional fixed subnetting uses the same mask everywhere, which wastes IPs. For example, if every subnet is /24 but one department only needs 10 IPs, you've wasted 244 addresses. With VLSM, you give that department a /28 (14 hosts) instead, saving the remaining IPs for subnets that need them. It's the industry standard approach for efficient network design."

---

**Q6: What is the difference between SNMPv2 and SNMPv3?**

> "SNMPv2 uses plain-text community strings — basically passwords that go across the network unencrypted. The default strings 'public' for read and 'private' for write are widely known. An attacker on the same network can capture these and either read all device information or even reconfigure devices. SNMPv3 adds authentication (ensures the message really came from who it claims) and encryption (no one can read the community string or data in transit). SNMPv3 should always be used — SNMPv1 and v2 should be disabled."

---

**Q7: What is the network address and broadcast address of 10.0.0.0/8?**

> "Network address is 10.0.0.0 — all host bits set to 0. Broadcast is 10.255.255.255 — all host bits set to 1. The usable range is 10.0.0.1 to 10.255.255.254, giving 16,777,214 usable host addresses. This is a Class A private range per RFC 1918."

---

**Q8: Why do WAN links between two routers typically use /30?**

> "A /30 gives 4 total addresses — network address, two usable hosts, and broadcast. That's exactly what you need for a point-to-point link: one IP for each router. Using /24 or /25 would waste hundreds of addresses on a link that only connects two devices. /30 is the most efficient choice. In modern service provider networks, /31 is increasingly used — it gives only 2 addresses with no network or broadcast address (per RFC 3021), saving one more IP per link."

---

**Q9: What is the difference between DNS A record and CNAME record?**

> "An A record maps a domain name directly to an IPv4 address — for example, google.com maps to 142.250.80.46. A CNAME (Canonical Name) record maps a domain alias to another domain name — not directly to an IP. For example, www.google.com might CNAME to google.com, which then has the A record. This is useful because you only need to update the IP in one place (the A record) and all CNAMEs pointing to it automatically get the new IP. CNAMEs cannot point to an IP address directly."

---

**Q10: You see a lot of traffic on port 53 UDP going to an external IP that isn't your DNS server. What is this likely?**

> "This is suspicious DNS traffic and suggests either DNS tunnelling or communication with a rogue DNS server. DNS tunnelling is a technique attackers use to exfiltrate data — they hide information inside DNS query names (e.g., sensitivedata.evil.com sends 'sensitivedata' to the attacker's domain). Because DNS is usually allowed outbound through firewalls, it's a common exfiltration technique. I would investigate the external IP (is it a known bad actor?), look at the volume and frequency of queries, check the length of the domain names in queries (tunnelling uses unusually long subdomains), and potentially block external DNS and force all DNS through an internal server."

---

# 🔬 PART 6 — HANDS-ON PRACTICE

## Try These Right Now!

### Subnetting Practice Problems

Solve these before looking at answers:

```
Problem 1: What is the subnet, network address, broadcast, and range for 10.0.0.55/28?

Problem 2: How many usable hosts in a /21 network?

Problem 3: You need 50 subnets from 192.168.0.0/24. What mask do you use?

Problem 4: What is the summary route for:
           192.168.4.0/24, 192.168.5.0/24, 192.168.6.0/24, 192.168.7.0/24?

Problem 5: 172.16.45.200/20 — find network address and broadcast.
```

### Answers:

```
Problem 1: /28 = block size 16
  Subnets: 0, 16, 32, 48, 64...
  55 falls between 48 and 64 → subnet 10.0.0.48/28
  Network: 10.0.0.48, Broadcast: 10.0.0.63, Range: .49-.62, Hosts: 14

Problem 2: /21 = host bits = 32-21 = 11
  Hosts = 2^11 - 2 = 2048 - 2 = 2046 usable hosts

Problem 3: 50 subnets from /24
  Need 2^n ≥ 50 → 2^6 = 64 ≥ 50 → borrow 6 bits → /30
  But /30 only gives 2 hosts per subnet!
  Better interpretation: If you need 50 host subnets:
  Each subnet needs at least 1 host. /30 gives 2 hosts — barely useful.
  More practical: /26 gives 4 subnets each with 62 hosts
  If question means 50 subnets from /24 that's /30 (64 subnets, 2 hosts each)

Problem 4: 
  192.168.4.0: 11000000.10101000.00000100.00000000
  192.168.5.0: 11000000.10101000.00000101.00000000
  192.168.6.0: 11000000.10101000.00000110.00000000
  192.168.7.0: 11000000.10101000.00000111.00000000
  Common: first 22 bits match → Summary: 192.168.4.0/22

Problem 5: /20 = block size 16 in 3rd octet
  Subnets: 172.16.0.x, 172.16.16.x, 172.16.32.x, 172.16.48.x...
  45 falls between 32 and 48 (32 ≤ 45 < 48) → 172.16.32.0/20
  Network: 172.16.32.0, Broadcast: 172.16.47.255
```

## Terminal Commands to Practice

```bash
# Check your IP address and subnet mask
ip addr show          (Linux)
ipconfig /all         (Windows)

# Check your routing table
ip route show         (Linux)
route print           (Windows)
netstat -rn           (Mac/Linux)

# DNS lookups
nslookup google.com
nslookup -type=MX gmail.com          (mail records)
nslookup -type=TXT google.com        (SPF records)
nslookup -type=NS google.com         (nameservers)
nslookup -type=PTR 8.8.8.8          (reverse DNS)
dig google.com ANY                    (Linux — all record types)

# Check open ports on your machine
netstat -an | grep LISTEN            (all listening ports)
netstat -an | grep ESTABLISHED       (active connections)
ss -tlnp                             (Linux — better than netstat)

# Test connectivity at different layers
ping 127.0.0.1                       (Layer 3 — loopback — tests your stack)
ping 192.168.1.1                     (Layer 3 — tests to your gateway)
ping 8.8.8.8                         (Layer 3 — tests internet connectivity)
ping google.com                      (Layer 3+7 — tests DNS + internet)

# Traceroute (see every router hop)
tracert google.com                   (Windows)
traceroute google.com                (Linux/Mac)
traceroute -U -p 53 google.com      (trace using UDP port 53)

# Check who's running on a port
netstat -ano | findstr :443          (Windows — what's on port 443?)
ss -tlnp | grep :443                 (Linux)
```

## Wireshark Filters for Ports and Protocols Practice

```
tcp.port == 80          See all HTTP traffic
tcp.port == 443         See all HTTPS traffic
tcp.port == 22          See all SSH connections
udp.port == 53          See all DNS queries
tcp.port == 3389        See all RDP connections
tcp.flags.syn == 1 && tcp.flags.ack == 0    Only SYN packets (new connections)
arp                     See ARP requests and replies
icmp                    See ping traffic
http.request.method == "GET"    Only HTTP GET requests
dns.qry.name contains "google"  DNS queries for google
```

---

# 📌 FINAL CHEAT SHEET — STICK THIS ON YOUR WALL

```
╔═══════════════════════════════════════════════════════════════╗
║            PORTS CHEAT SHEET                                  ║
╠═══════════════════════════════════════════════════════════════╣
║  20/21=FTP(insecure)  22=SSH/SFTP  23=Telnet(NEVER!)        ║
║  25=SMTP  53=DNS  67/68=DHCP  80=HTTP  110=POP3(insecure)  ║
║  143=IMAP(insecure)  161/162=SNMP  389=LDAP(insecure)       ║
║  443=HTTPS  445=SMB  636=LDAPS  993=IMAPS  995=POP3S        ║
║  3306=MySQL  3389=RDP(danger!)  8080=HTTP-alt               ║
╠═══════════════════════════════════════════════════════════════╣
║            SUBNETTING QUICK REFERENCE                         ║
╠═══════════════════════════════════════════════════════════════╣
║  /24 = 254 hosts  /25 = 126  /26 = 62  /27 = 30            ║
║  /28 = 14 hosts   /29 = 6    /30 = 2   /32 = 1             ║
║                                                               ║
║  Hosts per subnet = 2^(host bits) - 2                        ║
║  Block size = 2^(host bits)                                   ║
║  Magic number = 256 - subnet mask value                       ║
╠═══════════════════════════════════════════════════════════════╣
║            SUBNET MASK → CIDR QUICK MAP                       ║
╠═══════════════════════════════════════════════════════════════╣
║  255.255.255.0   = /24    255.255.255.128 = /25             ║
║  255.255.255.192 = /26    255.255.255.224 = /27             ║
║  255.255.255.240 = /28    255.255.255.248 = /29             ║
║  255.255.255.252 = /30    255.255.255.255 = /32             ║
╠═══════════════════════════════════════════════════════════════╣
║            PRIVATE IP RANGES (RFC 1918)                       ║
╠═══════════════════════════════════════════════════════════════╣
║  10.0.0.0/8         → Class A (16M hosts)                   ║
║  172.16.0.0/12      → Class B (1M hosts)                    ║
║  192.168.0.0/16     → Class C (65K hosts, home networks!)   ║
║  127.0.0.1          → Loopback (yourself)                   ║
║  169.254.x.x        → APIPA (DHCP failed!)                  ║
╚═══════════════════════════════════════════════════════════════╝
```

---
