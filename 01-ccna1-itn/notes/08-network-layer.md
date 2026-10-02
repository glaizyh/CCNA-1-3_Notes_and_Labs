# Module 8: Network Layer

## 1. Network Layer Characteristics

### Role of the Network Layer
- Provides services that let end devices exchange data across the network.
- **IPv4** and **IPv6** are the principal network layer protocols.
- Four basic operations:
  1. **Addressing end devices:** each device gets a unique IP address for identification.
  2. **Encapsulation:** wraps the Layer 4 segment into a Layer 3 PDU (the **IP packet**).
  3. **Routing:** directs packets toward a destination host on another network, through routers.
  4. **De-encapsulation:** at the destination host, opens the packet, verifies the header, and passes the Layer 4 PDU up.

### IP Encapsulation
- IP encapsulates the transport layer segment.
- IP can use an IPv4 or IPv6 packet without affecting the Layer 4 segment.
- Every Layer 3 device examines the IP packet as it crosses the network.
- IP addressing does **not** change from source to destination (NAT changes it, but that's covered in later modules).

### Characteristics of IP
IP has low overhead and three main properties:

**1. Connectionless**
- Doesn't establish a connection with the destination before sending.
- Needs no control information (synchronization, acknowledgments, etc.).
- The destination receives the packet without any advance notice.
- If connection-oriented traffic is needed, another protocol (typically TCP at the transport layer) handles it.

**2. Best effort**
- IP does **not guarantee delivery**.
- Lower overhead, because there's no mechanism to resend data that wasn't received.
- IP doesn't expect acknowledgments and doesn't know whether the destination is working.
- Unreliable on its own: IP can't manage, fix, retransmit, or realign undelivered, corrupt, or out-of-sequence packets.

**3. Media independent**
- Doesn't care what frame type the data link layer needs or what media the physical layer uses.
- Can be sent over any media: copper, fiber, or wireless.

### MTU and Fragmentation
- The network layer receives MTU (Maximum Transmission Unit) information from the data link layer and sets the MTU size.
- **Fragmentation:** Layer 3 splits an IPv4 packet into smaller units when it crosses media with a smaller MTU.
  - Fragmentation causes latency.
  - **IPv6 does not fragment packets** in routers.

## 2. IPv4 Packet

### IPv4 Header Characteristics
- The primary network layer communication protocol.
- Written in binary and read left to right, 4 bytes (32 bits) per line.
- Major header fields ensure correct routing and processing by Layer 3 devices.
- The two most important fields: **Source IP address** and **Destination IP address**.

### Significant IPv4 Header Fields

| Field | Description |
|-------|-------------|
| Version | 4-bit field identifying the packet as IPv4 (`0100`) |
| Differentiated Services (DS) | Used for QoS (formerly called Type of Service, ToS) |
| Header checksum | Detects corruption in the IPv4 header |
| Time to Live (TTL) | Layer 3 hop count limit. When TTL reaches zero, the router discards the packet |
| Protocol | Identifies the next upper-level protocol (ICMP, TCP, UDP) |
| Source IPv4 address | 32-bit address of the sending device |
| Destination IPv4 address | 32-bit address of the receiving device |

## 3. IPv6 Packet

### Limitations of IPv4
- **Address depletion:** IPv4 has a limited address space, and it has effectively run out.
- **Lack of end-to-end connectivity:** NAT was created to preserve IPv4, which ended direct end-to-end communication with public addresses.
- **Increased network complexity:** NAT was meant to be temporary; changing network headers causes latency and troubleshooting issues.

### IPv6 Overview and Improvements
- Developed by the IETF to overcome IPv4's limits.
- **Larger address space:** 128-bit addresses (340 undecillion) vs. IPv4's 32-bit addresses (about 4 billion).
- **Improved packet handling:** simplified header with fewer fields.
- **No need for NAT:** the huge address space removes the need for private-to-public translation.

### IPv6 Packet Header Fields
- The IPv6 header is fixed at **40 bytes**.
- Several IPv4 fields were removed to improve router performance: Flag, Fragment Offset, and Header Checksum.

| Field | Description |
|-------|-------------|
| Version | 4-bit field identifying the packet as IPv6 (`0110`) |
| Traffic Class | Used for QoS; equivalent to the IPv4 DS field |
| Flow Label | 20-bit field telling devices to handle packets with the same flow label in the same way |
| Payload Length | 16-bit field giving the size of the data portion of the packet |
| Next Header | Identifies the next-level protocol (ICMP, TCP, UDP); replaces the IPv4 Protocol field |
| Hop Limit | Layer 3 hop count limit; replaces the IPv4 TTL field |
| Source IPv6 address | 128-bit address of the sending device |
| Destination IPv6 address | 128-bit address of the receiving device |

### Extension Headers (EH)
- Optional fields placed between the IPv6 header and the payload.
- Carry optional network layer information for fragmentation, security, mobility support, and more.
- Routers don't fragment IPv6 packets; when needed, the sending host fragments using extension headers.

## 4. How a Host Routes

### Host Forwarding Decisions
1. Packets are always created at the source device.
2. Each host creates and maintains its own routing table.
3. A host can send packets to:
   - **Itself:** loopback address `127.0.0.1` (IPv4) or `::1` (IPv6)
   - **Local hosts:** the destination is on the same LAN
   - **Remote hosts:** the destination is on a different LAN

### Local vs. Remote Destinations
1. **IPv4:** the source host uses its own IP address and subnet mask with the destination IP address to decide whether the destination is local or remote.
2. **IPv6:** the source host uses the network address and prefix advertised by the local router.
3. **Local traffic:** sent directly out the host interface to an intermediary device (such as a switch).
4. **Remote traffic:** sent to the **default gateway** on the LAN.

### Default Gateway (DGW)
A router or Layer 3 switch interface on the local LAN.
- Must have an IP address in the same network range as the other devices on the LAN.
- Accepts traffic from the LAN and forwards traffic off the LAN toward remote networks.
- Has a route to other networks in its routing table.
- If a host has no default gateway (or the wrong one), its traffic **cannot leave the local LAN**.

### Gateway Discovery and Host Routing Tables
1. **IPv4 gateway discovery:** configured statically or assigned dynamically by DHCP.
2. **IPv6 gateway discovery:** learned dynamically through Router Solicitation (RS) and Router Advertisement (RA) messages, or configured manually.
3. **View a host routing table (Windows):** run `route print` or `netstat -r` in the command prompt.

The output has three sections:
1. Interface list (all network interfaces and MAC addresses)
2. IPv4 route table
3. IPv6 route table

## 5. Introduction to Routing

### Router Packet Forwarding Process
When a router receives a frame from a host:
1. The packet arrives on an interface (e.g., GigabitEthernet 0/0/0). The router **de-encapsulates** the Layer 2 header and trailer.
2. The router examines the packet's **destination IP address** and searches its routing table for the best match.
3. The router **encapsulates** the packet in a new Layer 2 frame and forwards it out the exit interface toward the next-hop router or the destination.

### Types of Routes in a Routing Table
- **Directly connected:** added automatically when an interface is active and has an IP address.
- **Remote routes:** networks not directly connected, learned either:
  - **Manually:** with static routes
  - **Dynamically:** with routing protocols that share network information between routers
- **Default route:** forwards all traffic to a specific next hop when no other route matches.

### Static Routing
- Configured manually by a network administrator.
- Must be updated manually whenever the topology changes.
- Ideal for small, non-redundant networks, or for setting a gateway of last resort alongside dynamic protocols.

```
R1(config)# ip route 10.1.1.0 255.255.255.0 209.165.200.226
```
(remote network address, subnet mask, next-hop IP)

### Dynamic Routing
- Automatically discovers remote networks, keeps routing information up to date, and chooses the best path.
- Automatically finds new best paths if the topology changes or a link fails.
- Can automatically share static default routes with other routers running the protocol.
- Example protocols: **OSPF**, **EIGRP**.

### Cisco IPv4 Routing Table Codes (`show ip route`)

| Code | Meaning |
|------|---------|
| **L** | Local interface IP address of the router (/32 mask) |
| **C** | Directly connected network |
| **S** | Static route configured by an administrator |
| **S\*** | Candidate default route (gateway of last resort) |
| **O** | OSPF dynamic route |
| **D** | EIGRP dynamic route |

## Exam Reminders
- IP is **connectionless, best effort, and media independent**.
- IPv4 = 32-bit address, variable header; IPv6 = 128-bit address, fixed 40-byte header.
- TTL (IPv4) is replaced by Hop Limit (IPv6); Protocol is replaced by Next Header.
- IPv6 routers don't fragment; the sending host does.
- A host uses the **default gateway** for any remote destination. No gateway = stuck on the LAN.
- Routing table codes: L, C, S, S*, O, D.
- Static route syntax: `ip route <network> <mask> <next-hop>`.
