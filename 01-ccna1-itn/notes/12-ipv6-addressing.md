# Module 12: IPv6 Addressing

## 1. IPv4 Issues

### Need for IPv6
- IPv4 is running out of addresses. IPv6 is its successor, with a much larger **128-bit** address space.
- IPv6 also includes fixes for IPv4's limitations and other enhancements.
- Reasons to move: a growing internet population, the limited IPv4 address space, NAT issues, and the Internet of Things (IoT).

**IPv4 exhaustion dates (RIRs)**

| RIR | Exhausted |
|-----|-----------|
| APNIC | April 2011 |
| RIPE NCC | September 2012 |
| LACNIC | June 2014 |
| ARIN | July 2015 |
| AfriNIC | Projected 2020 |

### IPv4 and IPv6 Coexistence
Both protocols will coexist for several years during the transition. The IETF created three migration categories:

| Method | Description |
|--------|-------------|
| **Dual stack** | Devices run the IPv4 and IPv6 stacks at the same time |
| **Tunneling** | Carries an IPv6 packet over an IPv4 network by encapsulating it inside an IPv4 packet |
| **Translation** | **NAT64** lets IPv6-enabled devices communicate with IPv4-enabled devices |

Tunneling and translation are only for the transition. The final goal is **native IPv6** from source to destination.

## 2. IPv6 Address Representation

### Addressing Formats
- IPv6 addresses are **128 bits** long, written in **hexadecimal**.
- Not case-sensitive (lowercase or uppercase).
- **Preferred format:** `x:x:x:x:x:x:x:x`, where each `x` is a **hextet** of four hex values.
- **Hextet:** the unofficial term for a 16-bit segment (four hex values).

Examples:
```
2001:0db8:0000:1111:0000:0000:0000:0200
2001:0db8:0000:00a3:abcd:0000:0000:1234
```

### Rule 1: Omit Leading Zeros
Drop any leading 0s in a hextet.

| Before | After |
|--------|-------|
| 01ab | 1ab |
| 09f0 | 9f0 |
| 0a00 | a00 |
| 00ab | ab |

Only **leading** zeros, never trailing ones (that would make the address ambiguous).

```
Preferred:        2001:0db8:0000:1111:0000:0000:0000:0200
No leading zeros: 2001:db8:0:1111:0:0:0:200
```

### Rule 2: Double Colon
A double colon (`::`) replaces **one** contiguous string of one or more all-zero hextets.

```
2001:db8:cafe:1:0:0:0:1  ->  2001:db8:cafe:1::1
```

- `::` can be used **only once** in an address, to avoid ambiguity.

```
Compressed: 2001:db8:0:1111::200
```

## 3. IPv6 Address Types

### Address Categories

| Type | Description |
|------|-------------|
| **Unicast** | Uniquely identifies an interface on an IPv6-enabled device |
| **Multicast** | Sends one IPv6 packet to multiple destinations |
| **Anycast** | A unicast address assigned to multiple devices; packets go to the nearest device sharing it |

IPv6 has **no broadcast address**, but the all-nodes multicast address gives essentially the same result.

### Prefix Length
- Shows the network portion in slash notation (`/x`).
- Can range from **/0 to /128**.
- Recommended for LANs and most networks: **/64** (64-bit prefix + 64-bit interface ID).
- A 64-bit interface ID is compatible with **SLAAC** and simplifies subnetting.

### Types of IPv6 Unicast Addresses
Devices typically have two unicast addresses:

1. **Global Unicast Address (GUA):** globally unique and internet-routable (like a public IPv4 address).
2. **Link-Local Address (LLA):** required on every IPv6-enabled device; used to communicate with other devices on the same link (not routable).

**Other unicast types**
- **Loopback:** `::1/128`
- **Unspecified:** `::/128`
- **Unique Local Address (ULA):** `fc00::/7` to `fdff::/7`
- **Embedded IPv4:** used by IPv4-to-IPv6 transition mechanisms

### Unique Local Address (ULA)
- Used for local addressing within a site or between a limited number of sites.
- Assigned to devices that will never need to reach another network.
- Not globally routed or translated to global IPv6 addresses.

### GUA Structure
- Currently assigned GUAs start with the bits `001` (first hextet from `2000` to `3fff`), which is **1/8** of the total address space.

| Part | Description |
|------|-------------|
| **Global routing prefix** | Assigned by the ISP to a customer or site |
| **Subnet ID** | Between the global routing prefix and the interface ID; used by organizations to identify subnets |
| **Interface ID** | The host portion, like in IPv4 (recommended 64 bits) |

The all-0s interface ID is reserved as the **Subnet-Router anycast address** for routers.

### Link-Local Address (LLA)
- Lets devices communicate on the same link/subnet only; packets can't be routed.
- LLAs are in the `fe80::/10` range.
- Used by routers for routing protocol updates, and by hosts as the default gateway address.

## 4. GUA and LLA Static Configuration

### Router Static GUA
```
R1(config)# interface gigabitethernet 0/0/0
R1(config-if)# ipv6 address 2001:db8:acad:1::1/64
R1(config-if)# no shutdown
```

### Host Static GUA
- Configured manually in the interface properties, like IPv4.
- The router's GUA or LLA can be the default gateway (best practice: use the **LLA**).

### Router Static LLA
```
R1(config)# interface gigabitethernet 0/0/0
R1(config-if)# ipv6 address fe80::1:1 link-local
R1(config-if)# no shutdown
```

The same LLA can be used on multiple router interfaces, as long as it is unique on each link.

## 5. Dynamic Addressing for IPv6 GUAs

### RS and RA Messages
Devices get GUAs dynamically through ICMPv6 messages:
- **Router Solicitation (RS):** sent by hosts to discover IPv6 routers.
- **Router Advertisement (RA):** sent every 200 seconds, or in answer to an RS, and carries the prefix, prefix length, default gateway, and DNS information.

### RA Assignment Methods

| Method | How the device gets its address |
|--------|---------------------------------|
| **1. SLAAC** | Builds its own GUA with no DHCPv6 server. The prefix comes from the RA; the interface ID is generated by the client (EUI-64 or random) |
| **2. SLAAC + stateless DHCPv6** | Builds its GUA with SLAAC and uses the RA source address as the default gateway. Asks a stateless DHCPv6 server for extra info (DNS server address, domain name) |
| **3. Stateful DHCPv6 (no SLAAC)** | Gets the GUA, prefix length, DNS servers, and domain name from a stateful DHCPv6 server. Uses the router's LLA (from the RA) as the default gateway |

### Interface ID Generation
**EUI-64** turns a 48-bit MAC address into a 64-bit interface ID:
1. Insert the 16-bit value `fffe` in the middle of the MAC address.
2. Invert the 7th bit of the MAC address (binary 0 to 1, or 1 to 0).

Example:
```
MAC address:   fc:99:47:75:ce:e0
Insert fffe:   fc:99:47:ff:fe:75:ce:e0
Flip 7th bit:  fe:99:47:ff:fe:75:ce:e0   (fc = 11111100, fe = 11111110)
Interface ID:  fe99:47ff:fe75:cee0
```

**Randomly generated:** operating systems such as Windows Vista and later generate a random 64-bit interface ID.

**Duplicate Address Detection (DAD):** how clients check that an address is unique on the link.

## 6. Dynamic Addressing for IPv6 LLAs
- Every interface must have an IPv6 LLA.
- **Dynamic LLA:** created automatically from the `fe80::/10` prefix plus a 64-bit interface ID (EUI-64 or random).
- **Cisco routers** automatically generate an LLA with EUI-64 whenever a GUA is assigned to an interface.

Verification commands:
```
show interface gigabitEthernet <port>
show ipv6 interface brief
```

## 7. IPv6 Multicast Addresses
Multicast addresses use the prefix `ff00::/8` and can only be **destination** addresses.

### Well-Known Multicast Addresses
Assigned to predefined groups:

| Address | Group |
|---------|-------|
| `ff02::1` | **All-nodes:** joined by all IPv6-enabled devices on the link |
| `ff02::2` | **All-routers:** joined by all IPv6 routers when `ipv6 unicast-routing` is enabled |

### Solicited-Node Multicast Addresses
- Mapped to a special Ethernet multicast address.
- Lets the Ethernet NIC filter frames by the destination MAC address, without passing them up to the IPv6 process.

## 8. Subnet an IPv6 Network

### Subnetting Strategy
- IPv6 uses the dedicated **16-bit Subnet ID** inside a /48 global routing prefix to create subnets.
- Structure: **48-bit global routing prefix + 16-bit subnet ID + 64-bit interface ID** (a /64 prefix).
- A 16-bit subnet ID allows **65,536** unique /64 subnets.

### Subnet Allocation Example
Given the prefix `2001:db8:acad::/48`:

| Subnet | Prefix |
|--------|--------|
| 0 | 2001:db8:acad:0000::/64 |
| 1 | 2001:db8:acad:0001::/64 |
| 2 | 2001:db8:acad:0002::/64 |
| 3 | 2001:db8:acad:0003::/64 |
| 4 | 2001:db8:acad:0004::/64 |
| 5 | 2001:db8:acad:0005::/64 |
| ... | ... |
| 65535 | 2001:db8:acad:ffff::/64 |

### Router Subnet Configuration Example
```
R1(config)# interface gigabitethernet 0/0/0
R1(config-if)# ipv6 address 2001:db8:acad:1::1/64
R1(config-if)# no shutdown
R1(config-if)# exit
R1(config)# interface gigabitethernet 0/0/1
R1(config-if)# ipv6 address 2001:db8:acad:2::1/64
R1(config-if)# no shutdown
R1(config-if)# exit
R1(config)# interface serial 0/1/0
R1(config-if)# ipv6 address 2001:db8:acad:3::1/64
R1(config-if)# no shutdown
```

## Exam Reminders
- IPv6 = 128 bits = 8 hextets. Remove leading zeros; use `::` once.
- GUA starts with `2000::/3` (2 or 3); LLA = `fe80::/10`; ULA = `fc00::/7`; multicast = `ff00::/8`.
- No broadcast in IPv6; use `ff02::1` (all nodes) instead.
- Typical LAN prefix = /64. Subnet ID = 16 bits inside a /48.
- SLAAC = no DHCPv6 server; stateful DHCPv6 = server gives the address.
- EUI-64: insert `fffe` and flip the 7th bit.
- Transition: dual stack, tunneling, NAT64.
