# Module 2: Single-Area OSPFv2 Configuration

## 1. OSPF Router ID

### Enabling OSPF
- **Global command:** `router ospf process-id`
- **Process ID:** a value from 1 to 65,535 chosen by the administrator. It is **locally significant**: it doesn't have to match on neighboring routers, although using the same ID everywhere is best practice.

### Purpose of the Router ID
- A 32-bit value written like an IPv4 address that uniquely identifies an OSPF router.
- Used in database synchronization during the Exchange state (the router with the highest RID sends its DBDs first).
- Used in the election of the Designated Router (DR) and Backup Designated Router (BDR).

### Router ID Selection Order
Cisco routers choose the Router ID by these criteria, in strict order:
1. **Explicit configuration:** the `router-id rid` command in router configuration mode (**recommended**).
2. **Highest loopback IPv4 address:** the highest IP address among the configured loopback interfaces.
3. **Highest active physical IPv4 address:** the highest IP address among the active physical interfaces.

### Loopback Interfaces as Router IDs
- Assigned with a `/32` subnet mask (`255.255.255.255`), which makes a host route.
- OSPF doesn't need to be running on the loopback interface for its IP address to be chosen as the Router ID.

### Modifying a Router ID
- An active Router ID doesn't change until the OSPF process is reset or the router reloads.
- The preferred way to apply a new Router ID is the privileged EXEC command `clear ip ospf process`.

## 2. Point-to-Point OSPF Networks

### Enabling OSPF on Interfaces

| Method | Where | Syntax | Notes |
|--------|-------|--------|-------|
| **`network` command** | Router config mode | `network network-address wildcard-mask area area-id` | The wildcard mask is `255.255.255.255` minus the subnet mask. To match one exact interface IP, use the quad-zero wildcard mask `0.0.0.0` |
| **`ip ospf` command** | Interface config mode | `ip ospf process-id area area-id` | Set directly on the interface, so no wildcard mask calculation is needed |

Example:
```
R1(config)# router ospf 10
R1(config-router)# router-id 1.1.1.1
R1(config-router)# network 10.10.1.0 0.0.0.255 area 0
```

### Passive Interfaces
- **Purpose:** stops OSPF Hello messages from being sent out an interface, while the interface's subnet is still advertised to OSPF neighbors.
- **Reasons to use it:** saves link bandwidth, reduces CPU use, and lowers the security risk from packet sniffing or rogue routing updates.
- **Command:** `passive-interface interface-id` (router configuration mode).

### Point-to-Point Network Type
- Cisco routers treat Ethernet interfaces as the `BROADCAST` network type by default, which triggers DR/BDR elections.
- On a link that connects only two routers, those elections are unnecessary overhead.
- **Command:** `ip ospf network point-to-point` (interface configuration mode) turns off DR/BDR elections on that link.

### Loopback Interface Advertising
- By default, OSPF advertises a loopback interface as a `/32` host route, whatever mask it has.
- To advertise the loopback's real subnet mask, configure `ip ospf network point-to-point` on the loopback interface.

## 3. Multiaccess OSPF Networks

### DR, BDR, and DROTHER Roles

| Role | Description |
|------|-------------|
| **DR** (Designated Router) | The central point for collecting and distributing LSAs on a multiaccess network. Uses multicast address **224.0.0.5** (all OSPF routers) |
| **BDR** (Backup Designated Router) | A passive listener that watches the DR and takes over if it fails |
| **DROTHER** | A router that is neither the DR nor the BDR. Sends updates to the DR and BDR using multicast address **224.0.0.6** (all designated routers) |

### Adjacency States in Multiaccess Networks
- **FULL/DR or FULL/BDR:** a fully adjacent relationship between a router and the DR or BDR.
- **2-WAY/DROTHER:** a normal neighbor relationship between two routers that are neither the DR nor the BDR. DROTHERs exchange Hellos but don't synchronize their LSDBs with each other.

### DR/BDR Election Rules
Decided in this order:
1. **Highest interface priority:** the router with the highest priority becomes DR and the second highest becomes BDR. The range is `0` to `255` and the default is `1`. A priority of `0` makes the interface **ineligible** to become DR or BDR.
2. **Highest Router ID:** the tie-breaker when priorities are equal.

### Non-Preemptive Election Behavior
- DR/BDR elections are **non-preemptive**.
- If a router with a higher priority or Router ID joins **after** the election, it does **not** replace the existing DR or BDR.
- If the DR fails, the BDR becomes the DR, and a new election picks a new BDR.
- **Change the priority with:** `ip ospf priority value` (interface configuration mode).

## 4. Modify Single-Area OSPFv2

### Metric and Cost Calculation
- OSPF's metric is **cost**. A lower cost means a more preferred path.

```
Cost = Reference bandwidth / Interface bandwidth
```

- The default reference bandwidth is 10^8 bps (100 Mbps).
- OSPF rounds up to the nearest whole number, so every interface of **100 Mbps or faster** (Fast Ethernet, Gigabit Ethernet, 10 Gigabit Ethernet) gets a default cost of **1**.

### Adjusting the Reference Bandwidth
- To tell high-speed links apart, change the reference bandwidth on **all** routers in the OSPF domain.
- **Command:** `auto-cost reference-bandwidth Mbps` (router configuration mode).

| Fastest link | Command |
|--------------|---------|
| Gigabit Ethernet (1,000 Mbps) | `auto-cost reference-bandwidth 1000` |
| 10 Gigabit Ethernet (10,000 Mbps) | `auto-cost reference-bandwidth 10000` |

### Manual Cost Configuration
- You can set the cost by hand on specific interfaces, to override the calculated value or influence path selection.
- **Command:** `ip ospf cost value` (interface configuration mode).

### OSPF Timers (Hello and Dead Intervals)

| Timer | Meaning | Default |
|-------|---------|---------|
| **Hello interval** | How often Hello packets are sent | **10 seconds** (multiaccess and point-to-point links) |
| **Dead interval** | How long a router waits for a Hello before declaring a neighbor down | **4 times the Hello interval**, or **40 seconds** |

- The Hello and Dead intervals **must match** between neighbors for an adjacency to form.
- **Commands (interface configuration mode):**
  - `ip ospf hello-interval seconds`
  - `ip ospf dead-interval seconds`

## 5. Default Route Propagation

### Propagating a Default Route (ASBR)
- An edge router that connects OSPF to an external network or the internet is an **Autonomous System Border Router (ASBR)**.
- **Step 1:** configure an IPv4 default static route on the edge router:
  ```
  ip route 0.0.0.0 0.0.0.0 [next-hop-address | exit-intf]
  ```
- **Step 2:** propagate it through OSPF. In router configuration mode, enter:
  ```
  default-information originate
  ```
- On the other OSPF routers, the propagated default route shows in the routing table with the code **`O*E2`** (an External Type 2 route).

## 6. Verify Single-Area OSPFv2

### Key Verification Commands

| Command | What it verifies |
|---------|------------------|
| `show ip ospf neighbor` | Neighbor adjacencies, states (`FULL`, `2-WAY`), neighbor Router IDs, and interface roles (`DR`, `BDR`, `DROTHER`) |
| `show ip protocols` | The OSPF process ID, Router ID, advertised networks, enabled and passive interfaces, and the administrative distance (110) |
| `show ip ospf` | Process ID, Router ID, area details, the adjusted reference bandwidth, and when SPF last ran |
| `show ip ospf interface` | Detailed OSPF parameters per interface (network type, cost, Hello/Dead timers, DR/BDR IP addresses and Router IDs, neighbor count) |
| `show ip ospf interface brief` | A summary of OSPF-enabled interfaces: process ID, area, IP/mask, cost, state, and neighbor count (`F/C`) |
| `show ip route` | OSPF-learned routes (marked `O` or `O*E2`) |

### Neighbor Adjacency Failure Causes
Two routers won't form an OSPFv2 adjacency if any of these don't match:
- The subnet masks (the routers are on different subnets).
- The OSPF Hello or Dead timers.
- The OSPF network types (for example, point-to-point vs. broadcast).
- Missing or incorrect OSPF `network` statements or interface commands.

## Exam Reminders
- Router ID order: `router-id` command, then highest loopback IP, then highest active physical IP.
- Reset after changing the RID: `clear ip ospf process`.
- `network` command uses a **wildcard mask** (255.255.255.255 minus the subnet mask).
- Passive interface = no Hellos sent, but the subnet is still advertised.
- DR/BDR: highest priority wins (default 1, 0 = never), then highest Router ID. Elections are **non-preemptive**.
- Multicast addresses: 224.0.0.5 = all OSPF routers, 224.0.0.6 = all DRs.
- Cost = reference bandwidth / interface bandwidth; the default reference is 100 Mbps, so Fast and Gigabit Ethernet both cost 1.
- Hello 10 s / Dead 40 s by default, and they must match.
- Default route: static default route, then `default-information originate` (shows as `O*E2`).
