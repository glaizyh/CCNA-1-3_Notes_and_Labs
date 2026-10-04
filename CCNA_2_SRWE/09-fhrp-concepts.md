# Module 9: FHRP Concepts

## 1. First Hop Redundancy Protocols

### Default Gateway Limitations
- **Single point of failure:** hosts are configured with one default gateway IPv4 address.
- **Loss of connectivity:** if the default gateway's router interface fails, LAN hosts lose communication outside their local network.
- **Limitation:** the outage happens even if another redundant router exists on the network.
- **FHRP solution:** First Hop Redundancy Protocols provide alternate default gateways in switched networks that have multiple routers connected to the same VLANs.

### Router Redundancy and Operation
- **Virtual router:** several physical routers work together to look like **one virtual router** to the hosts.
- **Shared addressing:** routers in a redundancy group share a single virtual IP address and virtual MAC address.
- **ARP resolution:** hosts send ARP requests for the virtual default gateway IP and get the virtual router's MAC address in reply.
- **Transparent forwarding:** traffic sent to the virtual router is actually handled by the active forwarding router, so a failover is invisible to the hosts.

### Steps for Router Failover
1. **Loss of keepalives:** the standby router stops receiving the periodic Hello messages from the active forwarding router.
2. **Role assumption:** the standby router takes over as the primary forwarding router.
3. **Seamless service:** the new forwarding router takes over both the IPv4 and MAC addresses of the virtual router, so service isn't interrupted.

### FHRP Options Comparison

| Protocol | Description |
|----------|-------------|
| **HSRP** (Hot Standby Router Protocol) | Cisco-proprietary. Picks an active and a standby device for transparent failover |
| **HSRP for IPv6** | Cisco-proprietary. Gives HSRP functions in IPv6 networks, using a virtual MAC and IPv6 link-local addresses |
| **VRRPv2** (Virtual Router Redundancy Protocol v2) | Non-proprietary. Elects a virtual router master and backup routers for IPv4 networks |
| **VRRPv3** | Open standard. Supports IPv4 and IPv6, with better scalability and multi-vendor compatibility |
| **GLBP** (Gateway Load Balancing Protocol) | Cisco-proprietary. Protects traffic while also load balancing across a group of routers |
| **GLBP for IPv6** | Cisco-proprietary. Automatic backup and load sharing for IPv6 default gateways |
| **IRDP** (ICMP Router Discovery Protocol) | Legacy solution (RFC 1256) that lets IPv4 hosts find local routers |

## 2. HSRP Operation

### HSRP Overview
- **Purpose:** a Cisco-proprietary protocol built for high network availability and transparent failover.
- **Active router:** forwards packets sent to the virtual gateway address.
- **Standby router:** monitors the status of the HSRP group and takes over forwarding if the active router fails.

### Priority and Preemption
- **Election:** decides which router is active and which is standby.
- **Default priority:** **100** (range 0 to 255).
- **Highest priority wins** and becomes active. If priorities are equal, the router with the **highest IPv4 address** wins.

**Preemption**
- By default, a router that comes online later with a higher priority does **not** replace the current active router.
- Preemption must be enabled manually to force a new election when a higher-priority router boots up.
- Routers with equal priority but a higher IP address won't preempt an active router either.

### HSRP States and Timers

| State | Description |
|-------|-------------|
| **Initial** | Entered during configuration changes or when the interface starts up |
| **Learn** | Waiting to hear a hello from the active router to learn the virtual IP |
| **Listen** | Knows the virtual IP; listens for hellos from the active and standby routers |
| **Speak** | Sends periodic hellos and takes part in the election |
| **Standby** | Candidate to become the next active router; sends regular hellos |
| **Active** | Forwards the packets sent to the virtual gateway address |

| Timer | Default | Meaning |
|-------|---------|---------|
| **Hello** | 3 seconds | Sent to the HSRP multicast address |
| **Hold** | 10 seconds | The standby router becomes active if no hello arrives within this time |

**Timer tuning note:** don't set the hello timer below 1 second or the hold timer below 4 seconds, to avoid excessive CPU load.

## Exam Reminders
- FHRPs fix the single-default-gateway problem with a **virtual IP and virtual MAC** shared by a group of routers.
- Cisco-proprietary: HSRP, GLBP. Open standard: VRRP.
- HSRP: one **active** and one **standby** router. GLBP also load balances.
- HSRP default priority = 100; highest priority wins, then highest IP address.
- Preemption is **off** by default and has to be enabled.
- HSRP timers: hello 3 s, hold 10 s.
- Failover: the standby router stops hearing hellos, then takes over the virtual IP and MAC.
