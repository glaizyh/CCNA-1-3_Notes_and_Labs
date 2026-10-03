# Module 17: Build a Small Network

## 1. Devices in a Small Network

### Small Network Topologies
- Most businesses are small, so most business networks are small too.
- A small network design is usually simple.
- Small networks typically have a **single WAN connection** (DSL, cable, or Ethernet).
- Large networks need an IT department to maintain, secure, and troubleshoot devices and protect data. Small networks are managed by a local IT technician or a contracted professional.

### Device Selection
Like large networks, small networks need planning and design to meet user requirements. Planning makes sure requirements, cost factors, and deployment options are all considered. One of the first decisions is which **intermediary devices** to use.

Selection factors:
1. Cost
2. Speed and types of ports/interfaces
3. Expandability
4. Operating system features and services

### IP Addressing for a Small Network
1. **Addressing scheme:** create an IP addressing scheme and use it. Every host and device in an internetwork must have a **unique** address.
   - End user devices (number and type of connections: wired, wireless, remote access)
   - Servers and peripheral devices (e.g., printers and security cameras)
   - Intermediary devices, including switches and access points
2. **Best practice:** plan, document, and maintain the scheme **by device type**. A planned scheme makes it easier to identify device types and troubleshoot problems.

### Redundancy in a Small Network
- **Purpose:** maintain high reliability and remove single points of failure.
- **How:** install duplicate equipment, and provide duplicate network links for critical areas.

| Redundant component | Covers |
|---------------------|--------|
| Servers | Server failure |
| Links | Link failure (alternate paths) |
| Switches | Switch failure |
| Routers | Router or route failure |

### Traffic Management
- **Goal:** improve employee productivity and minimize network downtime.
- **QoS:** routers and switches in a small network should be configured to handle real-time traffic (voice and video) appropriately relative to other data traffic.
- **Priority queuing** has four queues:

| Queue | Traffic |
|-------|---------|
| High priority | Voice (always emptied first) |
| Medium priority | SMTP |
| Normal priority | Instant messaging |
| Low priority | FTP |

## 2. Small Network Applications and Protocols

### Common Applications
Software that provides network access takes two forms:
- **Network applications:** implement application layer protocols directly and communicate with the lower layers of the protocol stack.
- **Application layer services:** interface with the network and prepare data for transfer for programs that aren't network-aware.

### Common Protocols
Protocols support employee applications and services by defining communication processes, message types, syntax, field meanings, expected responses, and interaction with the lower layers.

| Protocol | Use |
|----------|-----|
| Telnet / SSH | Remote access to network devices and servers |
| HTTP / HTTPS | Between web clients and web servers |
| SMTP | Sending email |
| POP3 / IMAP | Clients retrieving email |
| FTP / SFTP | Downloading and uploading files between clients and servers |
| DHCP | Clients getting IP configuration from a DHCP server |
| DNS | Resolving domain names to IP addresses |

- **Multi-service servers:** one server can provide several services (e.g., email, FTP, and SSH at once).
- **Security policy:** many companies require secure protocol versions (SSH, SFTP, HTTPS) whenever possible.

### Voice and Video Applications
- Businesses increasingly use IP telephony and streaming media for customer communication and remote work.
- The administrator must make sure the right equipment is installed and configured to guarantee **priority delivery**.

Real-time factors:
1. **Infrastructure:** needs the capacity and capability to support real-time applications.
2. **VoIP:** cheaper than IP telephony, but with lower quality and fewer features.
3. **IP telephony:** uses dedicated servers for call control and signaling.
4. **Real-time applications:** need QoS to minimize latency. **RTP** (Real-Time Transport Protocol) and **RTCP** (Real-Time Transport Control Protocol) support them.

## 3. Scale to Larger Networks

### Small Network Growth
Scaling a small network as the business grows needs four things:
1. **Network documentation:** physical and logical topologies.
2. **Device inventory:** the devices that use or make up the network.
3. **Budget:** an itemized IT budget, including the fiscal-year equipment purchasing budget.
4. **Traffic analysis:** documented protocols, applications, services, and their traffic requirements.

### Protocol Analysis
To understand traffic flow patterns:
- Capture traffic during **peak** usage times for a proper picture.
- Capture on different network segments and devices (some traffic stays local).
- Evaluate the data by traffic **source, destination, and type** to improve management decisions.

### Employee Network Utilization
Documenting OS-built-in snapshots over time helps spot changing protocol requirements and traffic flows. Snapshots include:
- OS and OS version
- CPU, RAM, and drive utilization
- Non-network and network applications

## 4. Verify Connectivity

### Verify Connectivity with Ping
- The quickest way to test **Layer 3** connectivity between a source and destination IP address.
- Uses ICMP **Echo (Type 8)** and **Echo Reply (Type 0)** messages.

| OS | Behavior |
|----|----------|
| Windows 10 | Sends four ICMP echo messages and expects four replies |
| Cisco IOS | Sends five ICMP echo messages and shows an indicator for each reply |

**IOS ping indicators**

| Indicator | Meaning |
|-----------|---------|
| `!` | Echo reply received (validates the Layer 3 connection) |
| `.` | Timed out waiting for an echo reply (a problem on the network path) |
| `U` | A router on the path sent an ICMP Type 3 "destination unreachable" error |

### Extended Ping
- Enter it in privileged EXEC mode by typing `ping` with no destination address.
- Prompts let you customize parameters (target address, repeat count, datagram size, timeout, source address/interface).
- Press Enter to accept the default values.
- For IPv6, use `ping ipv6`.

### Verify Connectivity with Traceroute
- Finds Layer 3 problem areas along a path by returning a list of routed **hops**.

| OS | How it works | How to interrupt |
|----|--------------|------------------|
| Windows (`tracert`) | Sends ICMP Echo Requests | `Ctrl+C` |
| Cisco IOS (`traceroute`) / Linux | Uses UDP with invalid port numbers; the final destination returns an ICMP port unreachable message | `Ctrl+Shift+6` |

- An asterisk (`*`) means a request timed out because a router didn't respond, which can point to a path problem.

### Extended Traceroute
- **Windows:** customized with command-line options (see `tracert /?`), such as `-d`, `-h maximum_hops`, `-w timeout`, `-4`, `-6`.
- **Cisco IOS:** enter privileged EXEC mode and type `traceroute` with no IP address; prompts guide you through the settings.

### Network Baseline
- One of the most effective tools for monitoring and troubleshooting network performance.
- Built by copying `ping`, `trace`, or other command outputs into **time-stamped text files** kept in an archive for later comparison.
- Tracks error messages and host-to-host response times.

## 5. Host and IOS Commands

### IP Configuration Commands

**Windows 10**

| Command | Shows or does |
|---------|---------------|
| `ipconfig` | Basic IP settings (address, mask, default gateway) |
| `ipconfig /all` | MAC address and detailed Layer 3 settings |
| `ipconfig /release` and `ipconfig /renew` | Renews dynamic IP settings for DHCP clients |
| `ipconfig /displaydns` | DNS entries cached in memory |

**Linux**

| Command | Shows or does |
|---------|---------------|
| `ifconfig` | Active interface status and IP configuration |
| `ip address` | Shows, adds, or deletes addresses and properties |

**macOS**

| Command | Shows or does |
|---------|---------------|
| `ifconfig` | Checks interface IP settings from the CLI |
| `networksetup -listallnetworkservices` and `networksetup -getinfo <network service>` | Service verification tools |

### The `arp` Command
- Shows the IP-to-MAC address bindings in the host's ARP cache.
- Command: `arp -a` (Windows, Linux, or Mac).
- Clear the cache on Windows (needs administrator access): `netsh interface ip delete arpcache`

### Common Cisco IOS `show` Commands

| Command | Verifies |
|---------|----------|
| `show running-config` | Current configuration and settings |
| `show interfaces` | Interface status and error messages |
| `show ip interface` | Layer 3 interface information |
| `show ip interface brief` | Short summary of interface IP addresses, status, and protocol status |
| `show arp` | Known hosts on local Ethernet LANs |
| `show ip route` | Layer 3 routing information |
| `show protocols` | Operational protocols |
| `show version` | Memory, interfaces, licenses, and hardware details |
| `show cdp neighbors` | Neighbor device IDs, address list, port IDs, capabilities, and platform |
| `show cdp neighbors detail` | Neighbor IP addresses, to help find configuration errors |

## 6. Troubleshooting Methodologies

### Basic Troubleshooting Steps
1. **Identify the problem:** gather information through conversations with users and with tools.
2. **Establish a theory of probable causes:** work out several possible causes.
3. **Test the theory to determine the cause:** try quick fixes or do more research to confirm the root cause.
4. **Establish a plan of action and implement the solution.**
5. **Verify the solution and implement preventive measures:** confirm full functionality and prevent it happening again.
6. **Document findings, actions, and outcomes** for future reference.

### Problem Escalation
- Escalate a problem when it needs a manager's decision, specific technical expertise, or access the technician doesn't have.
- Company policy must clearly say when and how to escalate.

### The `debug` Command
- Shows OS processes, protocols, mechanisms, and event messages **in real time**, in privileged EXEC mode.
- `debug ?` lists the options.
- Turn it off with `no debug <command>` or `undebug <command>`. Use `undebug all` to stop all active debugging.
- **Caution:** heavy debug logging can overload the CPU and impair network functions or command processing.

### The `terminal monitor` Command
- Log messages and debug output are blocked on remote (VTY) connections by default.
- `terminal monitor` (privileged EXEC) shows log messages on a virtual terminal.
- `terminal no monitor` turns remote logging off.

## 7. Troubleshooting Scenarios

### Duplex Mismatch
Ethernet links work best when both ends use the same duplex mode.
- **Autonegotiation:** connected devices announce their capabilities and choose the highest-performance mode both support.
- **Duplex mismatch:** one end is full-duplex and the other half-duplex, causing poor performance, latency, and inefficiency.
- **Causes:** misconfigured interface settings or failed autonegotiation.

### IP Addressing Issues
- **IOS devices:** manual assignment mistakes cause communication failures. Check with `show ip interface` or `show ip interface brief`.
- **End devices (APIPA):** when a Windows host can't reach a DHCP server, it gives itself an address in `169.254.0.0/16` through **APIPA**. Hosts with APIPA addresses can't communicate with devices outside that range. Linux and macOS don't use APIPA.

### Default Gateway Issues
- The default gateway forwards traffic to remote networks. A misconfiguration prevents communication outside the local LAN.
- Check the host's gateway with `ipconfig` on Windows.
- Check a router's default route with `show ip route`.

### DNS Issues
- DNS addresses are assigned manually or through DHCP.
- **Cisco OpenDNS addresses** (filter phishing and malware):
  - Primary: `208.67.222.222`
  - Secondary: `208.67.220.220`
- Verify with `ipconfig /all` (active DNS servers) and `nslookup` (manual queries and DNS responses).

## Exam Reminders
- Ping tests Layer 3 with ICMP echo. IOS shows `!` (success), `.` (timeout), `U` (unreachable).
- Windows sends 4 pings; IOS sends 5.
- Windows `tracert` uses ICMP; IOS `traceroute` uses UDP. Interrupt IOS with `Ctrl+Shift+6`.
- A network baseline is time-stamped command output saved for comparison.
- 6 troubleshooting steps: identify, theory, test, plan and implement, verify and prevent, document.
- APIPA = 169.254.0.0/16 = no DHCP server reached.
- Duplex mismatch = one side half, one side full.
- Use `debug` carefully; turn it off with `undebug all`.
