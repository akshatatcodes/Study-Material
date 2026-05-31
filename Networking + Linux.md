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





# Network Topologies

Network topology refers to the physical or logical arrangement of devices and connections within a network. It determines how data flows between devices and how the network handles failures.

---

# Why Network Topology Matters

- Defines data flow paths.
- Affects network performance.
- Impacts scalability.
- Determines fault tolerance.
- Simplifies troubleshooting.

---

# Star Topology

The most common topology in modern networks.

### Structure

```text
        PC1
         |
PC2 --- Switch --- PC3
         |
        PC4
```

### Characteristics

- Central device (Switch/Hub) connects all devices.
- Also called Hub-and-Spoke topology.
- Easy to install and manage.
- Most Ethernet LANs use this design.

### Advantages

- Easy troubleshooting.
- Failure of one cable affects only one device.
- Scalable.

### Disadvantages

- Central switch becomes a single point of failure.

### Example

- Office LAN using an Ethernet switch.

---

# Ring Topology

Devices are connected in a circular path.

### Structure

```text
PC1 ---- PC2
 |        |
 |        |
PC4 ---- PC3
```

### Characteristics

- Data travels around the ring.
- Common in WANs and MANs.
- Provides redundancy.

### Advantages

- Predictable data flow.
- Can maintain connectivity through alternate paths.

### Disadvantages

- More complex than star topology.
- Failure can impact performance.

### Example

- Metro Ethernet networks.
- Service provider networks.

---

# Bus Topology

All devices share a single communication cable called a backbone.

### Structure

```text
PC1 --- PC2 --- PC3 --- PC4
          |
      Backbone
```

### Characteristics

- Single cable connects all devices.
- Popular in early Ethernet networks.
- Rare in modern LANs.

### Advantages

- Low cost.
- Simple design.

### Disadvantages

- Single cable failure can disrupt the network.
- Difficult troubleshooting.
- Limited scalability.

### Example

- Legacy Ethernet (10Base2, 10Base5).
- Automotive CAN Bus systems.

---

# Mesh Topology

Devices are connected through multiple paths.

### Structure

```text
     Site A
    /     \
   /       \
Site B --- Site C
```

### Characteristics

- Multiple interconnections.
- High redundancy.
- Provides fault tolerance.

### Advantages

- No single point of failure.
- Excellent reliability.
- Supports load balancing.

### Disadvantages

- Expensive.
- Complex management.

### Example

- ISP backbone networks.
- Enterprise WAN connections.
- SD-WAN deployments.

---

# Hybrid Topology

Combination of two or more topologies.

### Example

```text
Star LAN
    |
    |
Ring WAN
    |
    |
Star LAN
```

### Characteristics

- Flexible design.
- Common in large organizations.

### Advantages

- Combines strengths of different topologies.
- Highly scalable.

### Disadvantages

- More complex design and management.

### Example

- Corporate networks with multiple branch offices.

---

# Wireless Topologies

## Infrastructure Mode

Wireless devices communicate through an Access Point (AP).

### Structure

```text
Laptop
    \
     \
   Access Point
     /
    /
Mobile
```

### Characteristics

- Most common Wi-Fi deployment.
- Centralized communication.

### Advantages

- Easy management.
- Better security.
- Supports large numbers of devices.

### Example

- Home Wi-Fi.
- Office Wi-Fi.
- Public hotspots.

---

## Ad Hoc Mode

Devices communicate directly without an Access Point.

### Structure

```text
Laptop <----> Laptop
      <---->
       Mobile
```

### Characteristics

- Peer-to-peer communication.
- No central device required.

### Advantages

- Quick setup.
- No additional hardware.

### Disadvantages

- Limited range.
- Poor scalability.
- Less secure.

### Example

- Temporary file sharing.
- Emergency communication.

---

## Wireless Mesh

Wireless devices communicate with multiple neighboring devices.

### Structure

```text
Node A ---- Node B
   |           |
   |           |
Node C ---- Node D
```

### Characteristics

- Self-healing network.
- Multiple communication paths.
- Common in IoT environments.

### Advantages

- High availability.
- Automatic rerouting.
- Large coverage area.

### Disadvantages

- More complex configuration.

### Example

- Smart home devices.
- IoT sensor networks.
- Smart city deployments.

---

# Topology Comparison

| Topology | Cost | Scalability | Reliability | Common Usage |
|-----------|---------|---------|---------|---------|
| Star | Medium | High | High | LAN |
| Ring | Medium | Medium | High | WAN/MAN |
| Bus | Low | Low | Low | Legacy Networks |
| Mesh | High | High | Very High | ISP/WAN |
| Hybrid | Medium-High | High | High | Enterprise Networks |

---

# Quick Revision

### Star

```text
Central Switch
Most Common LAN Topology
```

### Ring

```text
Circular Path
Used for Redundancy
```

### Bus

```text
Single Backbone Cable
Legacy Ethernet
```

### Mesh

```text
Multiple Paths
Fault Tolerant
```

### Hybrid

```text
Combination of Topologies
```

### Wireless Infrastructure

```text
Uses Access Point
Most Common Wi-Fi
```

### Ad Hoc

```text
Device-to-Device
No Access Point
```

### Wireless Mesh

```text
Self-Healing
Common in IoT
```

---

# Exam Tips

| Topology | Key Point |
|-----------|------------|
| Star | Central Switch |
| Ring | Circular Data Flow |
| Bus | Single Backbone |
| Mesh | Multiple Paths |
| Hybrid | Combination Design |
| Infrastructure | Uses AP |
| Ad Hoc | No AP |
| Wireless Mesh | Self-Healing Network |
