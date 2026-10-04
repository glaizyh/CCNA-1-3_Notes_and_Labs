# Module 14: Routing Concepts

## 1. Path Determination

### Two Functions of a Router
- **Primary functions:** determine the best path for forwarding packets, based on the routing table, and forward packets toward their destination.
- **Routing process:** when an IP packet arrives on an interface, the router decides which interface to use to forward it to the destination.
- **Interfaces:** each network connected to a router typically needs a separate interface.

### Best Path = Longest Match
- **Longest match:** the route entry in the routing table with the greatest number of **far-left matching bits** with the packet's destination IP address.
- The longest match is always the preferred route.
- The **prefix length** sets the minimum number of far-left bits that must match between the packet's IP address and the route entry.

**IPv4 example:** destination `172.16.0.10` matches three route entries.

| Route entry | Match? |
|-------------|--------|
| `172.16.0.0/12` | Yes (12 bits) |
| `172.16.0.0/18` | Yes (18 bits) |
| `172.16.0.0/26` | Yes (26 bits, **longest match**, so it's chosen) |

**IPv6 example:** destination `2001:db8:c000::99/48`

| Route entry | Match? |
|-------------|--------|
| `2001:db8:c000::/40` | Yes (40 bits) |
| `2001:db8:c000::/48` | Yes (48 bits, **longest match**) |
| `2001:db8:c000:5555::/64` | No |

### Building the Routing Table
- **Directly connected networks:** added automatically when a local interface is configured with an IP address and prefix length and is activated (up and up).
- **Remote networks:** networks that aren't directly connected, learned through:
  - **Static routes:** manually configured entries.
  - **Dynamic routing protocols:** networks learned automatically.
- **Default route:** names a next-hop router to use when nothing else in the table matches (prefix length `/0`). Also called the **gateway of last resort**.

## 2. Packet Forwarding

### Decision Process
1. A frame arrives on an ingress interface.
2. The router examines the destination IP address in the packet header.
3. The router looks in its routing table for the **longest match**.
4. The router encapsulates the packet in a data link frame and forwards it out the egress interface.
5. If there is no matching route (and no default route), the packet is **dropped**.

### Forwarding Destination Types

| Destination | What the router does |
|-------------|----------------------|
| **Directly connected network** | Sends the packet straight to the destination device; finds the destination MAC address with ARP (IPv4) or Neighbor Discovery (IPv6) |
| **Remote network** | Sends the packet to the next-hop router; finds the next-hop router's MAC address |
| **No match** | Drops the packet |

### Packet Forwarding Mechanisms

| Mechanism | Description |
|-----------|-------------|
| **Process switching** | Older method. The CPU checks the routing table for **every single packet**, so it is slow |
| **Fast switching** | Uses a fast-switching cache. The first packet is process-switched by the CPU, then the flow information is cached so later packets skip the CPU |
| **Cisco Express Forwarding (CEF)** | The fastest and **default** method on Cisco IOS. Uses tables that update when something changes: the Forwarding Information Base (FIB) and the adjacency table |

## 3. Basic Router Configuration and Verification

### Essential Configuration Commands
```
Router> enable
Router# configure terminal
Router(config)# hostname R1
R1(config)# enable secret class
R1(config)# line console 0
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# logging synchronous
R1(config-line)# exit
R1(config)# line vty 0 4
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# transport input ssh telnet
R1(config-line)# exit
R1(config)# service password-encryption
R1(config)# banner motd # WARNING: Unauthorized access is prohibited! #
R1(config)# ipv6 unicast-routing

! Interface configuration example:
R1(config)# interface gigabitethernet 0/0/0
R1(config-if)# description Link to LAN 1
R1(config-if)# ip address 10.0.1.1 255.255.255.0
R1(config-if)# ipv6 address 2001:db8:acad:1::1/64
R1(config-if)# ipv6 address fe80::1:a link-local
R1(config-if)# no shutdown
R1(config-if)# exit
R1# copy running-config startup-config
```

### Verification Commands

| Command | Shows |
|---------|-------|
| `show ip interface brief` / `show ipv6 interface brief` | A brief interface status summary |
| `show running-config interface [interface-id]` | The running configuration of one interface |
| `show interfaces` / `show ip interface` | Detailed interface statistics and parameters |
| `show ip route` / `show ipv6 route` | The routing table |
| `ping` | Layer 3 connectivity |

### Filtering Command Output
Use the pipe (`|`) with `show` commands:

| Filter | Shows |
|--------|-------|
| `section` | The whole section that matches the expression |
| `include` | Only the lines that match the expression |
| `exclude` | Everything except the lines that match the expression |
| `begin` | Output starting at the first line that matches the expression |

## 4. IP Routing Table

### Route Source Codes

| Code | Meaning |
|------|---------|
| **L** | Local interface address (/32 for IPv4, /128 for IPv6) |
| **C** | Directly connected network |
| **S** | Static route |
| **O** | OSPF dynamic route |
| **\*** | Candidate for the default route |

### Routing Table Principles
1. Every router decides **on its own**, based on its own routing table.
2. One router's routing table doesn't necessarily match another router's.
3. Routing information about a path doesn't give you return routing information.

### Routing Table Entry Fields

| Field | Meaning |
|-------|---------|
| **Route source** | How the route was learned |
| **Destination network** | The network prefix and prefix length |
| **Administrative distance (AD)** | How trustworthy the source is (lower is better) |
| **Metric** | The value or cost to reach the destination (lower is better) |
| **Next hop** | The IP address of the next-hop router |
| **Route timestamp** | How long ago the route was learned |
| **Exit interface** | The egress interface used to forward the packet |

### Static Route Syntax
```
R1(config)# ip route 10.0.4.0 255.255.255.0 10.0.3.2
R1(config)# ipv6 route 2001:db8:acad:4::/64 2001:db8:acad:3::2
```

- **Default route values:** IPv4 is `0.0.0.0/0`; IPv6 is `::/0`.

### IPv4 vs. IPv6 Routing Table Structure
- **IPv4:** keeps the classful format, with **parent routes** (the classful network heading) and indented **child routes** (the subnets).
- **IPv6:** classless; all entries line up the same way.

### Administrative Distance (AD) Values

| Route source | AD |
|--------------|----|
| Directly connected | 0 |
| Static route | 1 |
| EIGRP summary | 5 |
| External BGP | 20 |
| Internal EIGRP | 90 |
| OSPF | 110 |
| IS-IS | 115 |
| RIP | 120 |
| External EIGRP | 170 |
| Internal BGP | 200 |

## 5. Static and Dynamic Routing

### Static vs. Dynamic Comparison

| Feature | Dynamic routing | Static routing |
|---------|-----------------|----------------|
| **Configuration complexity** | Independent of network size | Grows with network size |
| **Topology changes** | Adapts automatically | Needs manual changes |
| **Scalability** | High (simple to complex networks) | Low (simple networks) |
| **Security** | Must be configured | Inherent |
| **Resource usage** | Uses CPU, RAM, and bandwidth | No extra resources |
| **Path predictability** | Varies with the topology | Explicitly defined |

### Dynamic Routing Protocol Classification
- **Interior Gateway Protocols (IGP):** exchange routing information **within** an Autonomous System (AS).
  - *Distance vector:* RIPv2, EIGRP (IPv4) / RIPng, EIGRP for IPv6
  - *Link-state:* OSPFv2, IS-IS (IPv4) / OSPFv3, IS-IS for IPv6
- **Exterior Gateway Protocols (EGP):** exchange routing information **between** different autonomous systems.
  - *Path vector:* BGP-4 (IPv4) / BGP-MP (IPv6)

### Dynamic Protocol Components and Metrics
- **Components:** data structures (tables in RAM), protocol messages, and algorithms.

| Protocol | Metric |
|----------|--------|
| **RIP** | Hop count (maximum 15 hops) |
| **OSPF** | Cost (calculated from cumulative bandwidth) |
| **EIGRP** | Bandwidth and delay (optionally load and reliability) |

### Load Balancing
- **Equal-cost load balancing:** the router forwards packets over multiple paths with equal metric values. Dynamic protocols support it automatically, and it can be configured on static routes.
- **Unequal-cost load balancing:** supported **only** by EIGRP.

## Exam Reminders
- Best path = **longest match** (most matching far-left bits). No match and no default route = packet dropped.
- Routing table sources: directly connected, static, dynamic. A default route is `0.0.0.0/0` (IPv4) or `::/0` (IPv6).
- CEF is the default and fastest forwarding method; process switching is the slowest.
- Codes: **L** local, **C** connected, **S** static, **O** OSPF.
- Lower AD is more trusted: connected 0, static 1, EIGRP 90, OSPF 110, RIP 120.
- Metrics: RIP = hops (max 15), OSPF = cost, EIGRP = bandwidth and delay.
- IGP (within an AS): RIP, EIGRP, OSPF, IS-IS. EGP (between ASes): BGP.
- Only EIGRP supports unequal-cost load balancing.
- A router's routing table doesn't tell it how to get back; each router decides alone.
