# Module 11: IPv4 Addressing

## 1. IPv4 Address Structure

### Network and Host Portions
- An IPv4 address is a **32-bit hierarchical address** made of a **network portion** and a **host portion**.
- The **subnet mask** determines which part is the network and which part is the host.

### The Subnet Mask
- To find the network and host portions, the subnet mask is compared to the IPv4 address bit for bit, from left to right.
- The process used is called **ANDing**.

### The Prefix Length
- The prefix length is a shorter way to write a subnet mask, in **slash notation**.
- It is the **number of 1 bits** in the subnet mask. Count them and put a slash in front.

| Subnet mask | 32-bit binary | Prefix |
|-------------|---------------|--------|
| 255.0.0.0 | 11111111.00000000.00000000.00000000 | /8 |
| 255.255.0.0 | 11111111.11111111.00000000.00000000 | /16 |
| 255.255.255.0 | 11111111.11111111.11111111.00000000 | /24 |
| 255.255.255.128 | 11111111.11111111.11111111.10000000 | /25 |
| 255.255.255.192 | 11111111.11111111.11111111.11000000 | /26 |
| 255.255.255.224 | 11111111.11111111.11111111.11100000 | /27 |
| 255.255.255.240 | 11111111.11111111.11111111.11110000 | /28 |
| 255.255.255.248 | 11111111.11111111.11111111.11111000 | /29 |
| 255.255.255.252 | 11111111.11111111.11111111.11111100 | /30 |

### Determining the Network: Logical AND
A logical AND compares two bits. Only 1 AND 1 gives 1; every other combination gives 0.

| Bit 1 | Bit 2 | Result |
|-------|-------|--------|
| 1 | 1 | **1** |
| 1 | 0 | 0 |
| 0 | 1 | 0 |
| 0 | 0 | 0 |

To find the **network address**, AND the host IPv4 address with the subnet mask, bit by bit.

Example: 192.168.10.10 with mask 255.255.255.0
```
192.168.10.10   11000000.10101000.00001010.00001010
255.255.255.0   11111111.11111111.11111111.00000000
-------------   ------------------------------------
192.168.10.0    11000000.10101000.00001010.00000000   (network address)
```

### Network, Host, and Broadcast Addresses
Every network has these types of addresses:

| Address | Host bits |
|---------|-----------|
| **Network address** | All 0s |
| **First usable host** | All 0s, ending in a 1 |
| **Last usable host** | All 1s, ending in a 0 |
| **Broadcast address** | All 1s |

## 2. IPv4 Unicast, Broadcast, and Multicast
- **Unicast:** one packet to one destination IP address (one-to-one).
- **Broadcast:** one packet to all other destination addresses (one-to-all). A limited broadcast uses destination `255.255.255.255`.
- **Multicast:** one packet to a multicast group (one-to-many).

## 3. Types of IPv4 Addresses

### Public and Private IPv4 Addresses
- **Public addresses:** globally routed between ISP routers.
- **Private addresses:** blocks used by most organizations for internal hosts, defined in **RFC 1918**. They aren't globally unique and **can't be routed across the internet**.

| Network address and prefix | RFC 1918 private range |
|----------------------------|------------------------|
| 10.0.0.0/8 | 10.0.0.0 - 10.255.255.255 |
| 172.16.0.0/12 | 172.16.0.0 - 172.31.255.255 |
| 192.168.0.0/16 | 192.168.0.0 - 192.168.255.255 |

### Routing to the Internet (NAT)
- **NAT (Network Address Translation)** translates private IPv4 addresses to public IPv4 addresses.
- Usually enabled on the **edge router** that connects to the internet, to translate internal private addresses to public global addresses.

### Special Use IPv4 Addresses
- **Loopback (127.0.0.0/8):** usually 127.0.0.1; used on a host to test whether TCP/IP is working. Range: 127.0.0.1 to 127.255.255.254.
- **Link-local (169.254.0.0/16):** also called **APIPA** or self-assigned addresses. Range: 169.254.0.1 to 169.254.255.254. Used by Windows DHCP clients to configure themselves when no DHCP server is available.

### Legacy Classful Addressing
RFC 790 (1981) allocated IPv4 addresses in classes:

| Class | Range | Intended for | Networks | Hosts per network |
|-------|-------|--------------|----------|-------------------|
| **A** | 0.0.0.0/8 to 127.0.0.0/8 | Large networks | 128 | 16,777,214 |
| **B** | 128.0.0.0/16 to 191.255.0.0/16 | Medium networks | 16,384 | 65,534 |
| **C** | 192.0.0.0/24 to 223.255.255.0/24 | Small networks | 2,097,152 | 254 |
| **D** | 224.0.0.0 to 239.255.255.255 | Multicast | n/a | n/a |
| **E** | 240.0.0.0 to 255.255.255.255 | Experimental | n/a | n/a |

Classful addressing wasted many addresses, so it was replaced by **classless addressing**, which ignores the class rules.

### Assignment of IP Addresses
**IANA** manages blocks of IP addresses and allocates them to five **Regional Internet Registries (RIRs)**:

| RIR | Region |
|-----|--------|
| ARIN | North America |
| RIPE NCC | Europe, Middle East, Central Asia |
| APNIC | Asia Pacific |
| LACNIC | Latin America and Caribbean |
| AfriNIC | Africa |

RIRs allocate addresses to ISPs, who provide address blocks to smaller ISPs and organizations.

## 4. Network Segmentation

### Broadcast Domains and Segmentation
- Protocols like ARP and DHCP use broadcasts. Switches send broadcasts out all interfaces except the one they arrived on.
- **Routers stop broadcasts.** A router is the only device that doesn't propagate them.
- Each router interface connects to a **broadcast domain**, and broadcasts stay inside it.

### Problems with Large Broadcast Domains
- Hosts in a large broadcast domain can generate excessive broadcasts and hurt performance.
- **Solution:** make smaller broadcast domains by **subnetting**.

### Reasons for Segmenting Networks
- Reduces overall network traffic and improves performance.
- Lets you apply security policies between subnets.
- Limits the number of devices affected by abnormal traffic.
- Subnets can be organized by **location**, **group or function**, or **device type**.

## 5. Subnet an IPv4 Network

### Subnet on an Octet Boundary
Subnetting is easiest at the octet boundaries (/8, /16, /24). A longer prefix means more network bits and fewer host bits.

| Prefix | Subnet mask | Binary mask | Hosts per network |
|--------|-------------|-------------|-------------------|
| /8 | 255.0.0.0 | 11111111.00000000.00000000.00000000 | 16,777,214 |
| /16 | 255.255.0.0 | 11111111.11111111.00000000.00000000 | 65,534 |
| /24 | 255.255.255.0 | 11111111.11111111.11111111.00000000 | 254 |

### Subnet Within an Octet (Subnetting a /24)

| Prefix | Subnet mask | Binary mask | Subnets | Hosts per subnet |
|--------|-------------|-------------|---------|------------------|
| /25 | 255.255.255.128 | ...11111111.10000000 | 2 | 126 |
| /26 | 255.255.255.192 | ...11111111.11000000 | 4 | 62 |
| /27 | 255.255.255.224 | ...11111111.11100000 | 8 | 30 |
| /28 | 255.255.255.240 | ...11111111.11110000 | 16 | 14 |
| /29 | 255.255.255.248 | ...11111111.11111000 | 32 | 6 |
| /30 | 255.255.255.252 | ...11111111.11111100 | 64 | 2 |

## 6. Subnet a Slash 16 and a Slash 8 Prefix
- Subnetting a /16 or /8 means **borrowing bits** from the host octets to create subnets.
- **Number of subnets** = 2<sup>n</sup>, where *n* is the number of borrowed bits.
- **Number of hosts** = 2<sup>h</sup> - 2, where *h* is the number of remaining host bits (the 2 removed are the network and broadcast addresses).
- **Borrowing limit:** the last 2 host bits can't be borrowed, so there is room for valid host addresses.

## 7. Subnet to Meet Requirements

### Private vs. Public IPv4 Address Space
- **Intranet:** the internal enterprise network, using private IPv4 addresses.
- **DMZ (demilitarized zone):** internet-facing enterprise servers, configured with public IPv4 addresses.

### Planning Considerations
- Plan subnets based on the number of **host addresses needed per network** and the **total number of subnets** needed.
- Aim to **minimize unused host addresses** while **maximizing usable subnets**.

## 8. Variable Length Subnet Masking (VLSM)
- Traditional subnetting gives every subnet the same size, which wastes addresses on small links (for example, a point-to-point WAN link needs only 2 host addresses).
- **VLSM** divides a network into **unequal** parts by subnetting an already subnetted address.
- **Rule:** start by meeting the host requirements of the **largest** subnet first, then continue down to the smallest.

## 9. Structured Design

### IPv4 Network Address Planning
Scalable planning means studying network usage, working out the total subnets and hosts needed, defining DHCP/VLAN pools, and identifying the private vs. public boundaries.

### Device Address Assignment

| Device | Assignment |
|--------|------------|
| End user clients | Dynamic, through DHCP (reduces errors and management work) |
| Servers and peripherals | Predictable **static** IP addresses |
| Publicly accessible servers | Public IPv4 addresses, most often with NAT |
| Intermediary devices | Static addresses for management, monitoring, and security |
| Gateway devices | Router and firewall interfaces act as default gateways for local hosts |

## Exam Reminders
- Prefix length = number of 1 bits in the mask.
- Network address = host bits all 0. Broadcast = host bits all 1.
- Usable hosts = 2<sup>h</sup> - 2. Subnets = 2<sup>n</sup>.
- Private ranges: 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16.
- Loopback = 127.0.0.1. Link-local (APIPA) = 169.254.0.0/16.
- Routers stop broadcasts; switches don't.
- VLSM: allocate the largest subnet first.
