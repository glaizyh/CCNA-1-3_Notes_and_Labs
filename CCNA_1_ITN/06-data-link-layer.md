# Module 6: Data Link Layer

## 1. Purpose of the Data Link Layer

### The Data Link Layer Function
- Responsible for communication between end-device **NICs**.
- Lets upper layer protocols access the physical layer media, and **encapsulates Layer 3 packets** (IPv4 and IPv6) into **Layer 2 frames**.
- Performs **error detection** and rejects corrupt frames.

### IEEE 802 LAN/MAN Data Link Sublayers
The data link layer has two sublayers:

| Sublayer | Role |
|----------|------|
| **LLC** (Logical Link Control) | Communicates between networking software at the upper layers and device hardware at the lower layers (IEEE 802.2) |
| **MAC** (Media Access Control) | Handles data encapsulation and media access control |

MAC sublayer standards include Ethernet (IEEE 802.3), WLAN (IEEE 802.11), and WPAN (IEEE 802.15).

### Providing Access to Media
At each hop along the path, a router performs four basic Layer 2 functions:
1. Accepts a frame from the network medium.
2. De-encapsulates the frame to expose the encapsulated packet.
3. Re-encapsulates the packet into a new frame.
4. Forwards the new frame onto the medium of the next network segment.

### Data Link Layer Standards
Protocols are defined by engineering organizations:
- **IEEE:** Institute of Electrical and Electronics Engineers
- **ITU:** International Telecommunications Union
- **ISO:** International Organization for Standardization
- **ANSI:** American National Standards Institute

## 2. Topologies

### Physical and Logical Topologies
- **Topology:** the arrangement and relationship of network devices and the interconnections between them.
- **Physical topology:** shows the physical connections and how devices are interconnected.
- **Logical topology:** identifies virtual connections between devices using device interfaces and IP addressing schemes.

### WAN Topologies

| Topology | Description |
|----------|-------------|
| **Point-to-point** | Simplest and most common. A permanent link directly connecting two endpoints. Nodes don't share media with other hosts, so protocols can be very simple |
| **Hub and spoke** | Like a star: a central site interconnects branch sites through point-to-point links |
| **Mesh** | High availability, but every end system must connect to every other end system |

### LAN Topologies

| Topology | Description |
|----------|-------------|
| **Star / extended star** | Easy to install, highly scalable, easy to troubleshoot |
| **Bus** (legacy) | All end systems chained together and terminated on each end |
| **Ring** (legacy) | Each end system connects to its neighbors to form a ring |

### Duplex Communication Modes
- **Half-duplex:** only one device can send **or** receive at a time on a shared medium (WLANs and legacy bus topologies with hubs).
- **Full-duplex:** both devices can send **and** receive at the same time (Ethernet switches operate in full-duplex).

### Access Control Methods

**Contention-based access:** all nodes operate in half-duplex and compete for the medium.

| Method | Used on | How it works |
|--------|---------|--------------|
| **CSMA/CD** (Collision Detection) | Legacy bus-topology Ethernet LANs | If devices transmit at the same time, a collision occurs. Devices detect it, wait a random time, and retransmit |
| **CSMA/CA** (Collision Avoidance) | IEEE 802.11 WLANs | Transmitting devices include time duration information so other devices know how long the medium will be unavailable |

**Controlled access:** deterministic. Each node has its own designated time on the medium (legacy networks such as Token Ring and ARCNET).

## 3. Data Link Frame

### Frame Structure
Data is encapsulated at Layer 2 with a **header** and a **trailer** to form a frame. A frame has three main parts: **Header**, **Data (packet)**, and **Trailer**.

### Frame Fields

| Field | Description |
|-------|-------------|
| Frame start and stop | Identifies the beginning and end of the frame |
| Addressing | Indicates the source and destination nodes |
| Type | Identifies the encapsulated Layer 3 protocol |
| Control | Identifies flow control services |
| Data | Contains the frame payload |
| Error detection | Used to determine transmission errors |

### Layer 2 Addresses
- Also called **physical addresses**.
- Contained in the frame **header**.
- Used only for **local delivery** of a frame on a link.
- **Updated by each device** that forwards the frame along its path.

### Data Link Protocols
The logical topology and physical media determine which data link protocol is used:
1. Ethernet
2. 802.11 Wireless (WLAN)
3. Point-to-Point Protocol (PPP)
4. High-Level Data Link Control (HDLC)
5. Frame Relay


- Switches use full-duplex; WLANs and hubs use half-duplex.
- WAN topologies: point-to-point, hub and spoke, mesh.
