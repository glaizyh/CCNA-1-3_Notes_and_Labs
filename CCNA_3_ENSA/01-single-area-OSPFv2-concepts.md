# Module 1: Single-Area OSPFv2 Concepts

## 1. OSPF Features and Characteristics

### Overview of OSPF
- **Protocol type:** a link-state **Interior Gateway Protocol (IGP)**, designed as an alternative to RIP.
- **Advantages over RIP:** faster convergence, and it scales to much larger networks.
- **Areas:** OSPF divides the routing domain into areas, which controls the amount of routing update traffic.
- **Link:** a router interface, a network segment connecting two routers, or a stub network (such as an Ethernet LAN).
- **Link state:** information about a link, including its network prefix, prefix length, and cost.

### Components of OSPF

**5 types of OSPF packets**
1. **Hello:** discovers neighbors and builds adjacencies.
2. **Database Description (DBD):** checks that databases are synchronized.
3. **Link-State Request (LSR):** asks for specific link-state records.
4. **Link-State Update (LSU):** sends the specific link-state records that were requested.
5. **Link-State Acknowledgment (LSAck):** acknowledges received packets.

**3 OSPF databases (tables)**

| Database | Contents | Command | Same on every router? |
|----------|----------|---------|-----------------------|
| **Adjacency database** (neighbor table) | All neighbor routers with two-way communication | `show ip ospf neighbor` | No, unique per router |
| **Link-state database, LSDB** (topology table) | Information about all the other routers in the network | `show ip ospf database` | **Yes, identical for all routers in an area** |
| **Forwarding database** (routing table) | The best routes, produced by running the SPF algorithm on the LSDB | `show ip route` | No, unique per router |

### Dijkstra's Shortest Path First (SPF) Algorithm
- Calculates the shortest path to each destination from the **cumulative cost**.
- Builds an **SPF tree** with the local router at the root.
- The best routes from the SPF tree go into the routing table.

### Generic Link-State Operation
1. Establish neighbor adjacencies.
2. Exchange link-state advertisements (LSAs).
3. Build the link-state database (LSDB).
4. Run the SPF algorithm.
5. Choose the best route.

### Single-Area vs. Multiarea OSPF
- **Single-area OSPF:** all routers are in one area (best practice: **Area 0**).
- **Multiarea OSPF:** a hierarchical design in which every area connects to the backbone area (**Area 0**) through **Area Border Routers (ABRs)**.

**Advantages of multiarea OSPF**
- **Smaller routing tables:** addresses can be summarized between areas.
- **Less link-state update overhead:** needs less memory and CPU.
- **Fewer SPF calculations:** topology changes stay local, because LSA flooding stops at the area boundary.

### OSPFv3 Overview
- The equivalent of OSPFv2 for IPv6 prefixes.
- Runs as processes independent of OSPFv2.
- Uses IPv6 as the network layer transport and uses the SPF algorithm.

## 2. OSPF Packets

### OSPF Packet Types

| Type | Packet | Primary function |
|------|--------|------------------|
| **1** | **Hello** | Discovers neighbors and builds adjacencies |
| **2** | **DBD** (Database Description) | Checks database synchronization between routers |
| **3** | **LSR** (Link-State Request) | Requests specific link-state records |
| **4** | **LSU** (Link-State Update) | Sends the requested link-state records and forwards updates |
| **5** | **LSAck** (Link-State Acknowledgment) | Acknowledges received packets |

**LSU vs. LSA:** an LSU packet **contains** LSA messages. One LSU can hold up to 11 different OSPFv2 LSA types.

### OSPF Hello Packet
- Sent to the IPv4 multicast address **224.0.0.5** (all OSPF routers).
- **Functions:**
  1. Discover OSPF neighbors and establish adjacencies.
  2. Advertise the parameters that neighbors **must agree on** (Hello/Dead intervals, area ID, network mask).
  3. Elect the **Designated Router (DR)** and **Backup Designated Router (BDR)** on multiaccess networks. Point-to-point links don't need a DR or BDR.

## 3. OSPF Operation

### OSPF Operational States

| # | State | Description |
|---|-------|-------------|
| 1 | **Down** | No Hello packets received yet. The router sends its first Hello packets |
| 2 | **Init** | A Hello arrived from a neighbor and it contains the sending router's Router ID |
| 3 | **Two-Way** | Two-way communication is established. On multiaccess links, the DR and BDR are elected |
| 4 | **ExStart** | The routers set master/slave roles and initial sequence numbers for the DBD exchange |
| 5 | **Exchange** | The routers exchange DBD packets (which contain LSA headers) |
| 6 | **Loading** | The routers use LSRs and LSUs to request and receive the full LSA information. Routes are calculated with the SPF algorithm |
| 7 | **Full** | The link-state databases of both routers are fully synchronized |

### DR and BDR Roles in Multiaccess Networks

**Why a DR is needed.** Multiaccess networks (such as Ethernet) have two problems:
1. **Multiple adjacencies:** the number of adjacencies grows quickly with the number of routers, as n(n-1)/2.
2. **Extensive LSA flooding:** uncontrolled flooding creates too much network traffic.

**Roles**

| Role | Description |
|------|-------------|
| **DR** (Designated Router) | The central point for collecting and distributing LSAs |
| **BDR** (Backup Designated Router) | Takes over if the DR fails |
| **DROTHERS** | Routers that are neither the DR nor the BDR |

- **Election:** the router with the **highest priority** becomes the DR, and the second highest becomes the BDR. If priorities are equal, the **highest Router ID** wins.
- The DR is only used to spread LSAs. Traffic is still forwarded to the best next-hop router shown in the routing table.

### LSDB Synchronization Steps
1. **Decide who goes first:** the router with the highest Router ID sends its DBD first.
2. **Exchange DBDs:** the routers swap database descriptions and acknowledge them with LSAck packets.
3. **Send LSRs and LSUs:** each router requests missing or newer entries with LSRs, and the answers come back in LSUs.
4. **Updates afterwards:** incremental LSUs are sent when the topology changes, or periodically every **30 minutes**.

## Exam Reminders
- OSPF = link-state IGP using Dijkstra's SPF; metric is cumulative **cost**.
- 5 packet types: Hello (1), DBD (2), LSR (3), LSU (4), LSAck (5).
- 3 databases: adjacency (neighbors), LSDB (topology, same on all routers in an area), routing table.
- Hello packets go to 224.0.0.5. Hello/Dead intervals, area ID, and network mask must match between neighbors.
- States in order: Down, Init, Two-Way, ExStart, Exchange, Loading, Full.
- DR/BDR election: highest priority, then highest Router ID. Not used on point-to-point links.
- Multiarea OSPF connects every area to Area 0 through ABRs.
