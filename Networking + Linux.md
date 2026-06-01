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











# Network Types & Architectures

Network architecture defines how devices communicate, share resources, and manage network services.

---

# Network Architectures

## Peer-to-Peer (P2P)

A decentralized network where every device can act as both a client and a server.

### Characteristics

- No dedicated server.
- Devices share resources directly.
- Easy and inexpensive to set up.
- Suitable for small networks.

### Advantages

- Low cost
- Simple setup
- No server required

### Disadvantages

- Difficult to manage
- Poor scalability
- Weaker security
- No centralized administration

### Example

```text
PC1 <----> PC2
  \         /
   \       /
      PC3
```

**Use Case:** Home networks, file sharing between a few computers.

---

## Client-Server

A centralized network where dedicated servers provide services to client devices.

### Characteristics

- Centralized management.
- Dedicated servers.
- Better security.
- Easier authentication and access control.

### Advantages

- Centralized administration
- Better security
- Easier backups
- Highly scalable

### Disadvantages

- Higher cost
- Requires server hardware
- Server failure can impact services

### Example

```text
      Server
         |
 -----------------
 |       |       |
PC1     PC2     PC3
```

**Use Case:** Enterprises, banks, universities.

---

# Geographic Network Types

Network classifications based on geographical coverage.

---

## PAN (Personal Area Network)

Small network around a single person.

### Range

```text
1 - 10 meters
```

### Technologies

- Bluetooth
- NFC
- Infrared
- USB

### Example

```text
Phone <----> Bluetooth Headset
```

### Use Cases

- Wireless earbuds
- Smartwatches
- Fitness trackers

---

## LAN (Local Area Network)

Connects devices within a limited area.

### Coverage

```text
Room, Home, Office, Building
```

### Characteristics

- High speed
- Privately owned
- Low latency

### Example

```text
PCs connected through a switch
```

### Use Cases

- Home networks
- Office networks

---

## WLAN (Wireless Local Area Network)

A LAN using wireless communication.

### Technology

```text
Wi-Fi (802.11)
```

### Example

```text
Laptop
    |
 Access Point
    |
Mobile
```

### Use Cases

- Home Wi-Fi
- Office Wi-Fi
- Public hotspots

---

## CAN (Campus Area Network)

Connects multiple LANs within a campus.

### Coverage

```text
University Campus
Corporate Campus
Hospital Campus
```

### Example

```text
Building A LAN
       |
       |
Building B LAN
       |
       |
Building C LAN
```

### Use Cases

- Universities
- Large organizations

---

## MAN (Metropolitan Area Network)

Connects networks across a city.

### Coverage

```text
Entire City
```

### Characteristics

- Larger than LAN
- Smaller than WAN

### Example

```text
Office A ----- Office B
      \         /
       \       /
      City Network
```

### Use Cases

- City government networks
- ISP metro networks

---

## WAN (Wide Area Network)

Connects networks across countries or continents.

### Coverage

```text
National
International
Global
```

### Characteristics

- Uses third-party carriers.
- Higher latency than LAN.
- Largest network type.

### Example

```text
India Office
      |
 Internet/MPLS
      |
USA Office
```

### Use Cases

- Corporate branch connectivity
- Internet

---

# Specialized Network Types

---

## SAN (Storage Area Network)

A dedicated high-speed network used for storage access.

### Characteristics

- Block-level storage.
- Appears as a local disk to the OS.
- Extremely fast.
- Used in data centers.

### Technologies

- Fibre Channel
- iSCSI
- FCoE

### Example

```text
Server
   |
SAN Switch
   |
Storage Array
```

### Advantages

- High performance
- High availability
- Centralized storage

### Use Cases

- Databases
- Virtualization
- Enterprise storage

---

## NAS (Network Attached Storage)

A storage device connected directly to a normal network.

### Characteristics

- File-level storage.
- Accessible over Ethernet.
- Easy deployment.

### Example

```text
PC
 \
  \
   Switch ---- NAS
  /
 /
Laptop
```

### Protocols

- SMB
- CIFS
- NFS

### Advantages

- Easy setup
- Shared storage
- Lower cost than SAN

### Use Cases

- File sharing
- Home storage
- Backup systems

---

# SAN vs NAS

| Feature | SAN | NAS |
|----------|----------|----------|
| Access Type | Block Level | File Level |
| Speed | Very High | Moderate |
| Cost | High | Lower |
| Network | Dedicated Storage Network | Standard Ethernet |
| Appears As | Local Disk | Shared Folder |

---

# VPN (Virtual Private Network)

Creates a secure encrypted tunnel over a public network.

### Purpose

- Secure communication
- Remote access
- Site connectivity
- Data protection

### Core Concept

```text
Data
  ↓
Encryption
  ↓
Internet
  ↓
Decryption
```

### Benefits

- Privacy
- Security
- Remote connectivity

---

## Site-to-Site VPN

Connects entire networks together.

### Example

```text
Branch Office
      |
   VPN Tunnel
      |
Head Office
```

### Use Cases

- Company branches
- Multi-office organizations

---

## Client-to-Site VPN (Remote Access VPN)

Connects individual users to a corporate network.

### Example

```text
Remote Employee
       |
   VPN Tunnel
       |
Corporate Network
```

### Use Cases

- Work from home
- Remote administration

---

# SD-WAN (Software Defined WAN)

A software-driven approach to managing WAN traffic.

### Traditional WAN

```text
Manual Configuration
Expensive MPLS Links
Fixed Routing
```

### SD-WAN

```text
Centralized Management
Automatic Path Selection
Traffic Optimization
```

### Features

- Software controlled
- Intelligent routing
- Multiple WAN links
- Application-aware traffic management

### Example

```text
          SD-WAN Controller
                 |
    ----------------------------
    |             |            |
 MPLS        Broadband      5G/LTE
```

### Advantages

- Lower WAN costs
- Better performance
- Simplified management
- Improved redundancy

---

# Quick Revision

## Network Architectures

| Type | Key Point |
|--------|-----------|
| P2P | No Dedicated Server |
| Client-Server | Centralized Management |

---

## Geographic Networks

| Type | Coverage |
|--------|----------|
| PAN | Personal Devices |
| LAN | Building |
| WLAN | Wireless LAN |
| CAN | Campus |
| MAN | City |
| WAN | Country/Global |

---

## Storage Networks

| Type | Purpose |
|--------|---------|
| SAN | High-Speed Storage Network |
| NAS | Network File Storage |

---

## VPN Types

| Type | Purpose |
|--------|---------|
| Site-to-Site | Connect Networks |
| Client-to-Site | Connect Users |

---

## SD-WAN

```text
Software Managed WAN
Automatic Path Selection
Uses MPLS + Internet + LTE
```

---

# Exam Tips

```text
PAN  → Personal Devices (Bluetooth)

LAN  → Home/Office Network

WLAN → Wireless LAN

CAN  → Campus Network

MAN  → City-Wide Network

WAN  → Global Network

SAN  → Block-Level Storage

NAS  → File-Level Storage

VPN  → Encrypted Tunnel

SD-WAN → Software Managed WAN
```



# WAN Termination

WAN Termination refers to the point where a customer's network connects to the service provider's network. Understanding WAN termination is important for network deployment and troubleshooting.

---

# Demarcation Point (Dmarc)

The **Demarcation Point (Dmarc)** is the physical boundary between the service provider's network and the customer's network.

### Purpose

- Defines ownership responsibility.
- Separates carrier equipment from customer equipment.
- Helps identify where network problems originate.

### Example

```text
Service Provider Network
           |
           |
        Dmarc
           |
           |
Customer Network
```

### Why It Matters

When troubleshooting:

- Issue before Dmarc → Service Provider responsibility.
- Issue after Dmarc → Customer responsibility.

---

# Customer Premises Equipment (CPE)

Equipment located on the customer's side of the Dmarc.

### Examples

- Routers
- Firewalls
- Modems
- CSU/DSU Devices
- WAN Edge Devices

### Diagram

```text
ISP Network
     |
   Dmarc
     |
     |
   Router (CPE)
     |
 Internal Network
```

### Characteristics

- Owned or managed by customer.
- Connects internal network to WAN.
- First device inside customer premises.

---

# Smartjack (NIU)

A **Smartjack** (Network Interface Unit - NIU) is an intelligent device installed by the service provider.

### Functions

- Signal conversion.
- Line monitoring.
- Remote diagnostics.
- Fault isolation.

### Diagram

```text
ISP Network
      |
  Smartjack
      |
   Router
      |
 Internal LAN
```

### Characteristics

- Powered device.
- Managed by service provider.
- Supports remote testing.
- Provides performance monitoring.

### Benefits

- Faster troubleshooting.
- Reduces onsite technician visits.
- Helps identify line problems remotely.

---

# WAN Connection Components

```text
Service Provider
        |
        |
    Smartjack
        |
      Dmarc
        |
     Router
        |
   Internal LAN
```

---

# Troubleshooting WAN Issues

## Step 1: Check Internal Equipment

Verify:

- Router status
- Interface status
- Cabling
- Power

---

## Step 2: Test at the Dmarc

Connect a testing device directly to the provider side of the Dmarc.

```text
Provider Network
      |
    Dmarc
      |
 Test Device
```

### Results

| Result | Conclusion |
|----------|-----------|
| Test Passes | Problem is in customer network |
| Test Fails | Problem is in provider network |

---

## Step 3: Use Smartjack Diagnostics

The service provider can:

- Monitor line status.
- Check signal quality.
- Run loopback tests.
- Identify circuit failures remotely.

---

# Dmarc vs Smartjack

| Feature | Dmarc | Smartjack |
|----------|----------|----------|
| Purpose | Network Boundary | Intelligent Interface |
| Ownership | Shared Boundary | Service Provider |
| Diagnostics | No | Yes |
| Power Required | No | Yes |
| Remote Monitoring | No | Yes |

---

# Quick Revision

### Dmarc

```text
Boundary Between ISP and Customer
Used to Determine Responsibility
```

### CPE

```text
Customer-Owned Equipment
Router, Firewall, Modem
```

### Smartjack (NIU)

```text
Carrier-Owned Device
Supports Remote Diagnostics
```

### Troubleshooting Rule

```text
Issue Before Dmarc  → ISP Problem

Issue After Dmarc   → Customer Problem
```

---

# Exam Tips

| Term | Remember |
|--------|-----------|
| Dmarc | ISP ↔ Customer Boundary |
| CPE | Customer Equipment |
| Smartjack | Intelligent WAN Interface |
| NIU | Network Interface Unit |
| Loopback Test | WAN Diagnostics |
| WAN Termination | Connection Point to ISP |
