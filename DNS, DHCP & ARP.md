# 🌐 DNS, DHCP & ARP — Easy Guide for Job Preparation

> **Who is this for?** Anyone learning networking basics for the first time or preparing for a job interview. Everything is explained in simple, plain English with real-life examples.

---

## 📚 Table of Contents

1. [DNS — Domain Name System](#1-dns--domain-name-system)
   - [What is DNS?](#-what-is-dns)
   - [How DNS Works Step by Step](#-how-dns-works-step-by-step)
   - [DNS Record Types](#-dns-record-types)
   - [DNS Hierarchy](#-dns-hierarchy)
   - [DNS Caching](#-dns-caching)
   - [Types of DNS Servers](#-types-of-dns-servers)
2. [DHCP — Dynamic Host Configuration Protocol](#2-dhcp--dynamic-host-configuration-protocol)
   - [What is DHCP?](#-what-is-dhcp)
   - [DORA Process](#-dora-process--the-4-step-handshake)
   - [DHCP Lease](#-dhcp-lease)
   - [DHCP Scope and Options](#-dhcp-scope-and-options)
   - [Static vs Dynamic IP](#-static-vs-dynamic-ip)
3. [ARP — Address Resolution Protocol](#3-arp--address-resolution-protocol)
   - [What is ARP?](#-what-is-arp)
   - [How ARP Works Step by Step](#-how-arp-works-step-by-step)
   - [ARP Table / ARP Cache](#-arp-table--arp-cache)
   - [Gratuitous ARP](#-gratuitous-arp)
   - [ARP Poisoning / Spoofing](#-arp-poisoning--spoofing-security-topic)
4. [How DNS + DHCP + ARP Work Together](#4-how-dns--dhcp--arp-work-together)
5. [Quick Cheat Sheet](#5-quick-cheat-sheet)
6. [Interview Tips](#6-interview-tips)

---

## 1. DNS — Domain Name System

---

### 🌍 What is DNS?

**Simple Explanation:**
Think of DNS as the **phone book of the internet**. You don't memorize your friend's phone number — you just search their name and get the number. Similarly, you type `www.google.com` in your browser and DNS finds the actual IP address (like `142.250.182.46`) for you automatically.

Without DNS, you'd have to memorize IP addresses for every website. That's impossible!

```
You type:         www.google.com
DNS translates:   www.google.com → 142.250.182.46
Browser connects: to 142.250.182.46
```

**Real-Life Example:**
When you open YouTube on your phone, your phone first asks DNS: "What is the IP address of youtube.com?" DNS replies with the IP, and then your phone connects. This all happens in **milliseconds** — so fast you never notice it.

**Port used by DNS:** `Port 53`
**Protocol:** UDP (for normal queries) / TCP (for large responses and zone transfers)

---

### 🔄 How DNS Works Step by Step

Let's say you type `www.google.com` in your browser.

```
Step 1: Browser Cache Check
        ↓
Step 2: OS Cache Check (hosts file)
        ↓
Step 3: Ask Recursive Resolver (your ISP's DNS server)
        ↓
Step 4: Resolver asks Root DNS Server → "Who handles .com?"
        ↓
Step 5: Root says → "Ask the .com TLD server"
        ↓
Step 6: Resolver asks .com TLD Server → "Who handles google.com?"
        ↓
Step 7: TLD says → "Ask Google's Authoritative DNS server"
        ↓
Step 8: Google's DNS replies → "google.com = 142.250.182.46"
        ↓
Step 9: Resolver sends the IP to your browser
        ↓
Step 10: Browser connects to 142.250.182.46 ✅
```

**Detailed Diagram:**

```
Your Browser
     |
     | 1. "What is google.com?"
     ↓
Recursive Resolver (ISP DNS)
     |
     | 2. Ask Root Server
     ↓
Root DNS Server (.)
     |
     | 3. "Ask .com TLD server"
     ↓
TLD DNS Server (.com)
     |
     | 4. "Ask Google's DNS"
     ↓
Authoritative DNS (google.com)
     |
     | 5. "IP = 142.250.182.46"
     ↓
Recursive Resolver
     |
     | 6. Sends IP to browser
     ↓
Your Browser → Connects to Google ✅
```

---

### 📋 DNS Record Types

These are the different types of entries stored in a DNS database. Each record type answers a different kind of question.

| Record | Full Name | Purpose | Example |
|---|---|---|---|
| **A** | Address Record | Maps domain → IPv4 address | `google.com → 142.250.182.46` |
| **AAAA** | IPv6 Address Record | Maps domain → IPv6 address | `google.com → 2607:f8b0::200e` |
| **CNAME** | Canonical Name | Alias — points one domain to another domain | `www.google.com → google.com` |
| **MX** | Mail Exchange | Tells where to send emails for a domain | `gmail.com → mail.google.com` |
| **NS** | Name Server | Tells which server is authoritative for the domain | `google.com → ns1.google.com` |
| **PTR** | Pointer Record | Reverse DNS — maps IP → domain name | `142.250.182.46 → google.com` |
| **TXT** | Text Record | Stores text info (used for email verification, security) | SPF, DKIM records |
| **SOA** | Start of Authority | Info about the DNS zone (admin, serial number) | Zone management |
| **SRV** | Service Record | Specifies location of services | VoIP, SIP servers |

#### 🧠 Easy Ways to Remember DNS Records

```
A     = Address (IPv4)        → Most common record
AAAA  = Address (IPv6)        → 4 times longer address, 4 A's
CNAME = Alias                 → Points to another name (not an IP)
MX    = Mail                  → Email delivery
NS    = Name Server           → Who's in charge of the domain
PTR   = Pointer (Reverse DNS) → IP → Domain (opposite of A record)
TXT   = Text                  → Extra info (security, verification)
```

---

### 🏛️ DNS Hierarchy

DNS is organized like a tree — starting from the root at the top, going down to specific domains.

```
                    . (Root)
                   / | \
                  /  |  \
               .com .org .net .in .uk
                |
             google.com
                |
           www.google.com
           mail.google.com
           drive.google.com
```

**Levels explained:**

| Level | Name | Example | Who manages it |
|---|---|---|---|
| Top | Root (.) | `.` (invisible dot) | ICANN / Root servers |
| 2nd | TLD (Top Level Domain) | `.com`, `.org`, `.in` | Registries (Verisign for .com) |
| 3rd | Second Level Domain | `google`, `youtube` | The company/person who bought it |
| 4th | Subdomain | `www`, `mail`, `drive` | The domain owner |

**13 Root DNS Servers** exist worldwide (operated by organizations like NASA, MIT, ICANN). They don't store all records — they just direct queries to the right TLD servers.

---

### ⚡ DNS Caching

**Simple Explanation:**
After DNS resolves a domain name, it **saves (caches) the answer temporarily** so it doesn't have to do the full lookup every time. This makes browsing faster.

**TTL (Time To Live):**
Every DNS record has a TTL value — it tells how long (in seconds) the answer should be cached.

```
TTL = 3600 seconds = Cache the answer for 1 hour

After 1 hour → cache expires → must ask DNS again
```

**Where caching happens:**
1. **Browser cache** — Your browser remembers DNS lookups
2. **OS cache** — Your operating system remembers
3. **ISP Resolver cache** — Your ISP's DNS server caches answers for all their customers

**Why TTL matters:**
- Low TTL (60 seconds) = Changes propagate quickly but more DNS traffic
- High TTL (86400 = 24 hours) = Less traffic but changes take longer to update worldwide

---

### 🖥️ Types of DNS Servers

| Server Type | Role | Example |
|---|---|---|
| **Recursive Resolver** | Does the full lookup on your behalf | Your ISP's DNS, Google DNS (8.8.8.8) |
| **Root Server** | Directs to the right TLD server | 13 root servers worldwide |
| **TLD Server** | Handles a specific TLD (.com, .org) | Verisign manages .com |
| **Authoritative Server** | Has the actual final answer | Google's DNS for google.com |

**Popular Public DNS Servers:**

| Provider | DNS Server |
|---|---|
| Google | `8.8.8.8` and `8.8.4.4` |
| Cloudflare | `1.1.1.1` and `1.0.0.1` |
| OpenDNS | `208.67.222.222` |
| BSNL (India) | ISP-assigned |

---

## 2. DHCP — Dynamic Host Configuration Protocol

---

### 📦 What is DHCP?

**Simple Explanation:**
When you connect to Wi-Fi, your device needs an IP address to communicate on the network. DHCP is the system that **automatically gives your device an IP address** (and other settings) without you having to type anything manually.

Imagine joining a new office. Instead of you finding your own desk, the office manager (DHCP server) assigns you a desk (IP address) automatically. When you leave, the desk is given to someone else.

**Without DHCP:** You'd have to manually type an IP address on every device — phones, laptops, TVs, printers — every time they connect. Impossible in large networks!

**What DHCP provides (the full package):**

```
IP Address        → e.g., 192.168.1.100
Subnet Mask       → e.g., 255.255.255.0
Default Gateway   → e.g., 192.168.1.1 (your router)
DNS Server        → e.g., 8.8.8.8 (Google DNS)
Lease Time        → How long you can keep this IP
```

**Port used by DHCP:**
- Server listens on `Port 67`
- Client listens on `Port 68`

---

### 🤝 DORA Process — The 4-Step Handshake

DHCP works through 4 messages. Remember the acronym: **D.O.R.A.**

```
D → Discover
O → Offer
R → Request
A → Acknowledge
```

**Step-by-step with a real example:**

Imagine you just connected your laptop to a new Wi-Fi network.

---

**Step 1: DISCOVER** 🔍
Your laptop doesn't have an IP yet. It shouts to the entire network:
> *"Hello everyone! I just joined! I need an IP address. Is there a DHCP server here?"*

This is a **broadcast** — sent to all devices on the network.

```
💻 Laptop ——————————————————→ 📡 Broadcast (255.255.255.255)
            "Anyone there? I need an IP!"
```

---

**Step 2: OFFER** 📬
The DHCP server hears the broadcast and replies:
> *"Yes! I'm the DHCP server. I can offer you IP address 192.168.1.100. Want it?"*

```
💻 Laptop ←—————————————————— 🖥️ DHCP Server
          "I offer you 192.168.1.100"
```

---

**Step 3: REQUEST** ✋
Your laptop replies (still broadcast, in case multiple servers replied):
> *"Yes please! I accept the offer from that DHCP server. I'd like 192.168.1.100."*

```
💻 Laptop ——————————————————→ 📡 Broadcast
          "I accept! Please give me 192.168.1.100"
```

---

**Step 4: ACKNOWLEDGE** ✅
The DHCP server confirms:
> *"Done! 192.168.1.100 is yours. Here are also your subnet mask, gateway, and DNS settings. Lease is for 24 hours."*

```
💻 Laptop ←—————————————————— 🖥️ DHCP Server
          "Confirmed! IP is yours for 24 hours."
```

---

**Full DORA Diagram:**

```
💻 Client                          🖥️ DHCP Server
    |                                      |
    |——— DISCOVER (broadcast) ————————————→|
    |                                      |
    |←——— OFFER (IP: 192.168.1.100) ———————|
    |                                      |
    |——— REQUEST (I want that IP!) ————————→|
    |                                      |
    |←——— ACKNOWLEDGE (It's yours!) ————————|
    |                                      |
  ✅ Laptop now has IP: 192.168.1.100
```

---

### ⏰ DHCP Lease

**Simple Explanation:**
The IP address given by DHCP is not permanent — it's given for a **fixed time period called a lease**. After the lease expires, the device must renew it.

Think of it like renting an apartment. You get the apartment for 12 months. Before it expires, you renew the lease. If you don't, someone else can get that apartment.

```
Lease Time = 24 hours (typical for home networks)

At 50% of lease time → Client tries to renew with original server
At 87.5% of lease time → Client tries to renew with any DHCP server
At 100% (lease expired) → Client must start DORA process again
```

**Why leases matter:**
- Prevents IP address exhaustion
- Allows IPs to be reclaimed from devices that left the network
- Ensures IP pool stays fresh

---

### ⚙️ DHCP Scope and Options

**DHCP Scope:**
The **pool of IP addresses** that the DHCP server can hand out.

```
Example Scope:
Start IP:  192.168.1.100
End IP:    192.168.1.200
Total:     101 IP addresses available to assign
```

**DHCP Exclusions:**
Some IPs are reserved and excluded from the pool (for servers, printers, routers that need fixed IPs).

```
Scope:    192.168.1.100 – 192.168.1.200
Excluded: 192.168.1.100 – 192.168.1.110 (reserved for servers/printers)
Available: 192.168.1.111 – 192.168.1.200
```

**DHCP Reservations:**
Bind a specific IP to a specific device using its MAC address. The device always gets the same IP — like a permanent seat assignment.

```
MAC: AA:BB:CC:DD:EE:FF → Always gets IP 192.168.1.150
```

---

### 🔵 Static vs Dynamic IP

| Feature | Static IP | Dynamic IP (DHCP) |
|---|---|---|
| Assigned by | Manually by admin | Automatically by DHCP |
| Changes | Never changes | Can change on reconnection |
| Best for | Servers, printers, routers | Laptops, phones, guest devices |
| Management effort | High | Low |
| Cost | Higher (from ISP) | Lower |

**Real-Life Example:**
- Your home Wi-Fi router has a **static public IP** (or DHCP from ISP with long lease) so you can always reach it.
- Your laptop gets a **dynamic IP** from the router via DHCP every time it connects.

---

## 3. ARP — Address Resolution Protocol

---

### 🔍 What is ARP?

**Simple Explanation:**
On a network, computers communicate using **IP addresses** (like 192.168.1.100), but at the hardware level (inside the same network), they actually talk using **MAC addresses** (like `AA:BB:CC:DD:EE:FF`).

ARP is the protocol that **translates IP addresses to MAC addresses**.

Think of it like this:
- You know your friend's **name** (IP address)
- But to reach them, you need their **exact house address** (MAC address)
- ARP is the process of looking up that exact address

```
IP Address  = Logical address (like your name)
MAC Address = Physical address (like your house address)
ARP         = The process of finding house address from name
```

**ARP operates at:** Layer 2 (Data Link Layer) and Layer 3 (Network Layer) — the bridge between them.

---

### 🔄 How ARP Works Step by Step

**Scenario:** Your laptop (192.168.1.10) wants to send data to your printer (192.168.1.50), but it doesn't know the printer's MAC address.

---

**Step 1: ARP Request (Broadcast)**
Your laptop shouts to everyone on the network:
> *"Hey everyone! Who has IP address 192.168.1.50? Please tell me your MAC address!"*

```
💻 Laptop (192.168.1.10)
         |
         |——→ ARP Request (Broadcast to all devices)
         |    "Who has 192.168.1.50? Tell 192.168.1.10"
         |
    [All devices on the network receive this]
```

---

**Step 2: ARP Reply (Unicast)**
The printer recognizes its own IP and replies **directly** (unicast) to your laptop:
> *"That's me! My MAC address is AA:BB:CC:DD:EE:FF"*

```
🖨️ Printer (192.168.1.50)
         |
         |——→ ARP Reply (directly to laptop)
              "192.168.1.50 is at MAC: AA:BB:CC:DD:EE:FF"
```

---

**Step 3: Communication Begins**
Your laptop now knows the MAC address. It saves it in the **ARP cache** and sends the data directly to the printer.

```
💻 Laptop ——————————————————————→ 🖨️ Printer
     IP: 192.168.1.10         IP: 192.168.1.50
     MAC: 11:22:33:44:55:66   MAC: AA:BB:CC:DD:EE:FF

     Data sent directly using MAC address ✅
```

---

**Full ARP Diagram:**

```
💻 Laptop                    All Devices on Network
    |                               |
    |—— ARP Request (broadcast) ——→|
    |   "Who has 192.168.1.50?"    |
    |                               |
    |                         🖨️ Printer recognizes its IP
    |                               |
    |←—— ARP Reply (unicast) ——————|
    |   "I'm at AA:BB:CC:DD:EE:FF" |
    |                               |
    | Saves to ARP Cache            |
    |                               |
    |————— Data Transfer ——————————→|
             (using MAC address)
```

---

### 🗃️ ARP Table / ARP Cache

**Simple Explanation:**
After ARP resolves an IP to MAC, it **saves the result** in a table called the **ARP cache** (or ARP table). This avoids repeating the lookup every single time.

```
ARP Cache Example:

IP Address       MAC Address          Type
192.168.1.1      AA:BB:CC:11:22:33   dynamic
192.168.1.50     AA:BB:CC:DD:EE:FF   dynamic
192.168.1.100    11:22:33:44:55:66   static
```

**Dynamic entries:** Learned automatically by ARP, expire after a timeout (usually 2–20 minutes).

**Static entries:** Manually added by an admin, don't expire. Used for critical devices.

**How to view ARP table (command):**
```bash
# Windows / Linux / Mac
arp -a
```

**How to clear ARP cache:**
```bash
# Windows
arp -d *

# Linux
sudo ip neigh flush all
```

---

### 📣 Gratuitous ARP

**Simple Explanation:**
A **Gratuitous ARP** is when a device sends an ARP announcement **about itself**, without anyone asking. It's like walking into a room and announcing:
> *"Hey everyone! I'm here! My IP is 192.168.1.10 and my MAC is AA:BB:CC:DD:EE:FF. Update your records!"*

**When is it used?**
1. When a device **first connects** to the network — to announce its presence
2. When a device **changes its IP address** — to update everyone's ARP cache
3. **Failover/HA (High Availability)** — when a backup device takes over from a failed primary device, it sends a gratuitous ARP so all other devices update their tables

```
💻 Device connects → sends Gratuitous ARP
                      "I exist! IP=192.168.1.10, MAC=AA:BB:CC:..."
                            ↓
                    All devices update ARP cache
```

---

### ⚠️ ARP Poisoning / Spoofing (Security Topic)

**Simple Explanation:**
Since ARP has **no authentication** (it trusts whoever replies), a malicious device can send **fake ARP replies** to trick other devices into thinking the attacker's MAC address belongs to a legitimate IP address.

This is called **ARP Poisoning** or **ARP Spoofing** and is used in **Man-in-the-Middle (MITM) attacks**.

**How it works:**

```
Normal:
💻 Laptop → sends traffic to → 🖥️ Router (192.168.1.1)

After ARP Poisoning:
💻 Laptop → sends traffic to → 😈 Attacker (pretending to be router)
                                      ↓
                               Attacker forwards to router
                               (reading all your data in between!)
```

**Prevention:**
- **Dynamic ARP Inspection (DAI)** — Switch feature that validates ARP packets
- **Static ARP entries** — Manually map IPs to MACs for critical devices
- **802.1X** — Port-based network access control
- **VPN / Encryption** — Even if intercepted, data is unreadable

---

## 4. How DNS + DHCP + ARP Work Together

**The Full Story — From connecting to Wi-Fi to loading a website:**

Let's say you just turned on your laptop and want to open `www.google.com`.

```
STEP 1 — DHCP (Getting an IP address)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💻 Laptop connects to Wi-Fi
         ↓
DORA Process with DHCP Server:
Laptop gets:
  → IP:      192.168.1.100
  → Gateway: 192.168.1.1
  → DNS:     8.8.8.8


STEP 2 — DNS (Finding Google's IP)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
You type: www.google.com
         ↓
Laptop asks DNS server (8.8.8.8):
"What is the IP of www.google.com?"
         ↓
DNS replies: "142.250.182.46"


STEP 3 — ARP (Finding the Gateway's MAC)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
To send data to Google (outside the local network),
laptop must first send traffic to the Gateway (192.168.1.1)
         ↓
ARP Request: "Who has 192.168.1.1?"
ARP Reply:   "192.168.1.1 is at MAC AA:BB:CC:11:22:33"
         ↓
Laptop now sends data to gateway using that MAC address


STEP 4 — Data reaches Google ✅
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Gateway forwards packet to internet → Google → Page loads!
```

---

## 5. Quick Cheat Sheet

### 🗂️ DNS

| Item | Detail |
|---|---|
| Purpose | Translates domain names to IP addresses |
| Port | 53 (UDP for queries, TCP for transfers) |
| A Record | Domain → IPv4 |
| AAAA Record | Domain → IPv6 |
| CNAME | Alias → another domain |
| MX Record | Email server for a domain |
| PTR Record | IP → Domain (reverse DNS) |
| TTL | How long to cache a DNS answer |
| Recursive Resolver | Does the full lookup for you (e.g., 8.8.8.8) |
| Authoritative Server | Has the final answer for a domain |
| Root Servers | Top of the DNS tree — 13 worldwide |

---

### 🗂️ DHCP

| Item | Detail |
|---|---|
| Purpose | Automatically assigns IP settings to devices |
| Port | Server: 67 / Client: 68 |
| DORA | Discover → Offer → Request → Acknowledge |
| Lease | Time period an IP is assigned to a device |
| Scope | Pool of IPs available for assignment |
| Reservation | Fixed IP for a specific MAC address |
| What it gives | IP, Subnet Mask, Gateway, DNS, Lease Time |

---

### 🗂️ ARP

| Item | Detail |
|---|---|
| Purpose | Resolves IP addresses to MAC addresses |
| Layer | Works between Layer 2 and Layer 3 |
| ARP Request | Broadcast — "Who has this IP?" |
| ARP Reply | Unicast — "I have it! Here's my MAC" |
| ARP Cache | Table storing IP-to-MAC mappings |
| Gratuitous ARP | Device announces its own MAC unprompted |
| ARP Poisoning | Fake ARP replies used in MITM attacks |
| Command | `arp -a` to view ARP table |

---

### 🗂️ Ports Reference

| Protocol | Port | Transport |
|---|---|---|
| DNS | 53 | UDP / TCP |
| DHCP Server | 67 | UDP |
| DHCP Client | 68 | UDP |
| ARP | N/A | Operates at Layer 2 (no port) |

---

## 6. Interview Tips

### 🎯 Most Commonly Asked Questions

---

**Q1: What is DNS and how does it work?**
> DNS (Domain Name System) is like the internet's phone book — it translates human-readable domain names like `google.com` into IP addresses like `142.250.182.46`. When you type a URL, your browser queries a DNS resolver, which checks the cache, then queries root servers → TLD servers → authoritative servers until it gets the IP address, then returns it to your browser.

---

**Q2: What is the difference between A record and CNAME record?**
> An **A record** maps a domain directly to an **IPv4 address** (e.g., `google.com → 142.250.182.46`). A **CNAME record** is an alias — it maps one domain name to **another domain name** (e.g., `www.google.com → google.com`). CNAME cannot point to an IP address, only to another domain name.

---

**Q3: What is TTL in DNS?**
> TTL (Time To Live) is the time in seconds that a DNS record is cached by resolvers. A TTL of 3600 means the answer is cached for 1 hour. After that, a fresh lookup is needed. Low TTL = faster changes but more DNS traffic. High TTL = less traffic but slower updates.

---

**Q4: Explain the DORA process in DHCP.**
> DORA is the 4-step process:
> 1. **Discover** — Client broadcasts "I need an IP!" to the network
> 2. **Offer** — DHCP server offers an available IP address
> 3. **Request** — Client accepts the offer and requests that IP
> 4. **Acknowledge** — Server confirms and provides IP, subnet mask, gateway, and DNS settings

---

**Q5: What is a DHCP lease?**
> A DHCP lease is the time period for which a DHCP server assigns an IP address to a device. After the lease expires, the device must renew it or the IP can be reassigned to another device. Typical lease times range from a few hours to several days.

---

**Q6: What is the difference between a DHCP reservation and a static IP?**
> A **DHCP reservation** is configured on the DHCP server — it binds a specific IP to a device's MAC address, so the device always gets the same IP via DHCP. A **static IP** is configured directly on the device itself. Both achieve the same result (fixed IP), but reservations are easier to manage centrally.

---

**Q7: What is ARP and why is it needed?**
> ARP (Address Resolution Protocol) is needed because within a local network, devices communicate using MAC addresses (Layer 2), but network administrators and software use IP addresses (Layer 3). ARP bridges the gap by resolving "Who has this IP address?" into a MAC address so data frames can be properly addressed and delivered on the local network.

---

**Q8: What is the difference between ARP Request and ARP Reply?**
> An **ARP Request** is a **broadcast** — sent to all devices on the network asking "Who has IP X?" An **ARP Reply** is a **unicast** — sent directly from the device that has IP X back to the requester, saying "I have IP X, my MAC is Y."

---

**Q9: What is Gratuitous ARP?**
> A Gratuitous ARP is an ARP message sent by a device about itself — without being asked. It's used when a device joins a network (to announce its presence), when an IP changes, or during failover scenarios (like when a backup server takes over from a failed primary server and needs all devices to update their ARP caches).

---

**Q10: What is ARP Poisoning?**
> ARP Poisoning (or ARP Spoofing) is a Man-in-the-Middle attack where a malicious device sends fake ARP replies to associate its own MAC address with a legitimate IP address (like a router's IP). This causes traffic intended for the router to be sent to the attacker instead. Prevention includes Dynamic ARP Inspection (DAI) on switches and using encrypted protocols.

---

**Q11: How do DNS, DHCP, and ARP work together?**
> When a device connects to a network:
> 1. **DHCP** gives it an IP address, subnet mask, default gateway, and DNS server address
> 2. When the device wants to access a website, **DNS** resolves the domain name to an IP address
> 3. To send packets to the gateway (to reach external IPs), **ARP** resolves the gateway's IP to its MAC address so frames can be properly delivered on the local network

---

### 🧠 Golden Rules to Remember

```
DNS   → Domain Name = IP Address translation
        Port 53 | UDP for queries | "Phone book of the internet"

DHCP  → Auto-assigns IP + Gateway + DNS to devices
        Port 67 (server) / 68 (client)
        Remember DORA: Discover → Offer → Request → Acknowledge

ARP   → IP Address = MAC Address translation (local network)
        Broadcast to ask, Unicast to reply
        arp -a shows the ARP table

Together:
  DHCP gives you settings → DNS finds the IP → ARP finds the MAC → Data flows!
```

---

### 📝 Key Port Numbers to Memorize

```
DNS    = Port 53
DHCP   = Port 67 (server), 68 (client)
HTTP   = Port 80
HTTPS  = Port 443
FTP    = Port 21
SSH    = Port 22
Telnet = Port 23
SMTP   = Port 25
```

---

*📝 Notes prepared for networking job preparation. Topics cover CompTIA Network+, CCNA, and general IT networking fundamentals.*
