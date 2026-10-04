# Module 5: STP Concepts

## 1. Purpose of STP

### Redundancy and Layer 2 Loops
- **Redundancy** is an essential part of hierarchical design. It removes single points of failure and keeps services running.
- **Ethernet requirement:** Ethernet LANs need a loop-free topology, with a single physical path between any two devices.
- **Layer 2 loops:** redundant physical paths with no loop-prevention mechanism cause Layer 2 loops.

**Consequences of loops**
- Ethernet frames keep propagating forever.
- The MAC address table becomes unstable.
- Links become saturated.
- Switches and end devices hit high CPU utilization.
- The network becomes unusable.

### TTL / Hop Limit Differences
- **Layer 3 (IP):** the IPv4 TTL and IPv6 Hop Limit fields drop by one at each router. A packet is discarded when the counter reaches 0, which stops endless looping.
- **Layer 2 (Ethernet):** frames have **no TTL mechanism** (or anything like it) to limit retransmissions. Without STP, they loop until a link or switch fails. STP was developed specifically as the loop-prevention mechanism for Layer 2.

### Broadcast Storms
- **Definition:** an abnormally high number of broadcasts overwhelming the network during a specific time. It can disable a network within seconds.
- **Causes:** faulty hardware (such as a faulty NIC) or Layer 2 loops.
- **Frame types involved:** ARP requests, Layer 2 multicasts, and unknown unicast frames (flooded out all ports except the ingress port).
- **Note:** IPv6 packets are never forwarded as Layer 2 broadcasts. ICMPv6 Neighbor Discovery uses Layer 2 multicasts.

### The Spanning Tree Algorithm (STA)
- Invented by **Radia Perlman** at Digital Equipment Corporation (published in 1985).
- Creates a loop-free topology by choosing a single **root bridge** and finding the least-cost paths.
- Puts strategic ports in a **blocking state** to prevent data loops.
- **Recalculation:** STP unblocks ports automatically to open alternate paths when a cable fails, a switch fails, or a new switch or link is added.

## 2. STP Operations

### Building a Loop-Free Topology
1. Elect the **root bridge**.
2. Elect the **root ports**.
3. Elect the **designated ports**.
4. Elect the **alternate (blocked) ports**.

### Bridge Protocol Data Units (BPDUs)
- Frames that switches exchange every **2 seconds** after bootup to share spanning tree information.
- They carry the **Bridge ID (BID)** of the sending switch and the **Root ID** (the BID of the root bridge).

### Bridge ID (BID)

| Part | Description |
|------|-------------|
| **Bridge priority** | Default on Cisco switches is **32768**. Range is 0 to 61440 in steps of **4096**. Lower is preferred; priority 0 beats everything |
| **Extended system ID** | A decimal value added to the bridge priority to identify the VLAN for that BPDU instance. Example: default priority + VLAN 1 = 32768 + 1 = **32769** |
| **MAC address** | Tie-breaker when the priority and extended system ID are equal. The **lowest** hexadecimal MAC address wins |

### Step 1: Elect the Root Bridge
- The switch with the **lowest BID** becomes the root bridge.
- All switches start out declaring themselves root bridge, until BPDUs are exchanged.

### Step 2: Elect Root Ports
- Chosen on every **non-root switch**: one root port per non-root switch.
- It's the port closest to the root bridge, with the lowest **internal root path cost**.

**IEEE path cost values**

| Link speed | STP cost (802.1D-1998, short) | RSTP cost (802.1w-2004) |
|------------|-------------------------------|-------------------------|
| 10 Gbps | 2 | 2,000 |
| 1 Gbps | 4 | 20,000 |
| 100 Mbps | 19 | 200,000 |
| 10 Mbps | 100 | 2,000,000 |

Cisco switches use the IEEE 802.1D short path cost by default.

### Step 3: Elect Designated Ports
- One designated port per segment between two switches: the one with the best (lowest-cost) path toward the root bridge.
- **Rules:**
  - All ports on the root bridge are designated ports.
  - If one end of a segment is a root port, the other end is a designated port.
  - All ports connected to end devices are designated ports.

### Step 4: Elect Alternate (Blocked) Ports
- Any port that is neither a root port nor a designated port becomes an **alternate (blocked)** port.
- It is placed in a discarding/blocking state to prevent Layer 2 loops.

### Tie-Breaking for Equal-Cost Paths
When several equal-cost paths lead to the root bridge, a switch picks its root port in this order:
1. **Lowest sender BID**
2. **Lowest sender port priority** (default 128)
3. **Lowest sender port ID** (e.g., Fa0/1 beats Fa0/2 on the sending switch)

### STP Timers

| Timer | Purpose | Default | Range |
|-------|---------|---------|-------|
| **Hello** | Interval between BPDUs | 2 seconds | 1-10 s |
| **Forward delay** | Time spent in the Listening and Learning states | 15 seconds | 4-30 s |
| **Max age** | How long a switch waits before starting a topology change | 20 seconds | 6-40 s |

### STP Port States

| State | Receives BPDUs | Sends BPDUs | Updates MAC table | Forwards data frames |
|-------|----------------|-------------|-------------------|----------------------|
| **Blocking** | Yes | No | No | No |
| **Listening** | Yes | Yes | No | No |
| **Learning** | Yes | Yes | Yes | No |
| **Forwarding** | Yes | Yes | Yes | Yes |
| **Disabled** | No | No | No | No |

The disabled state is non-operational.

## 3. Evolution of STP

### STP Varieties and Implementations

| Version | Description |
|---------|-------------|
| **STP (802.1D)** | The original, legacy standard. Called Common Spanning Tree (CST): one instance for the whole network, no matter how many VLANs |
| **PVST+** | Cisco proprietary enhancement of 802.1D. A separate 802.1D instance **per VLAN**. Default on Cisco switches running IOS 15.0 or later |
| **802.1D-2004** | Updated STP standard that includes the 802.1w specifications |
| **RSTP (802.1w)** | Evolution of STP with faster convergence (a few hundred milliseconds) |
| **Rapid PVST+** | Cisco enhancement of RSTP: a separate 802.1w instance per VLAN |
| **MSTP (802.1s) / MST** | IEEE standard / Cisco implementation that maps multiple VLANs into one RSTP instance (up to 16 instances) |

### RSTP Port States
RSTP merges the 802.1D Disabled, Blocking, and Listening states into one **Discarding** state.

| 802.1D STP state | 802.1w RSTP state |
|------------------|-------------------|
| Disabled | Discarding |
| Blocking | Discarding |
| Listening | Discarding |
| Learning | Learning |
| Forwarding | Forwarding |

### RSTP Non-Forwarding Port Roles
- **Alternate port:** an alternate path to the root bridge.
- **Backup port:** a backup on a shared-medium segment (e.g., connected through a hub).

### PortFast and BPDU Guard

**PortFast**
- Configured on access ports connected to end devices (e.g., DHCP clients).
- Moves a port straight from blocking to forwarding, skipping the 30-second delay (listening and learning).
- **Warning:** enable it only on access ports. On inter-switch ports it risks loops.

**BPDU Guard**
- Protects PortFast-enabled access ports.
- If a BPDU arrives on a BPDU Guard port, the port goes immediately into the **err-disabled** state to prevent loops.
- It needs manual action by an administrator to bring the port back.

### Alternatives to STP
- Modern designs move from Layer 2 spanning tree loops to **Layer 3 routing** at the distribution and core layers.
- Layer 3 routing allows redundant, active physical paths without blocking ports.

## Exam Reminders
- Ethernet frames have no TTL, so Layer 2 loops need STP.
- Root bridge = **lowest BID** (priority, then MAC). Default priority 32768, steps of 4096.
- Election order: root bridge, root ports, designated ports, alternate ports.
- Costs (STP): 10 Gbps = 2, 1 Gbps = 4, 100 Mbps = 19, 10 Mbps = 100.
- Timers: hello 2 s, forward delay 15 s, max age 20 s. Blocking to forwarding takes about 30 to 50 s in classic STP.
- RSTP: Discarding, Learning, Forwarding; roles alternate and backup.
- PVST+ is the default; Rapid PVST+ = RSTP per VLAN.
- PortFast on access ports only, with BPDU Guard to protect it.
