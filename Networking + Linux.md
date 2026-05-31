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
