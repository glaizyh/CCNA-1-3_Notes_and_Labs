# Module 13: ICMP

## 1. ICMP Messages

### ICMPv4 and ICMPv6 Overview
- **ICMP (Internet Control Message Protocol)** gives feedback about problems with processing IP packets under certain conditions.
- **ICMPv4** is the messaging protocol for IPv4. **ICMPv6** is the one for IPv6, and it has extra functionality.
- Messages common to both:
  - Host reachability
  - Destination or service unreachable
  - Time exceeded
- **Security note:** ICMPv4 messages aren't required and are often blocked inside a network for security reasons. Many administrators limit or prohibit ICMP entering their network.

### Host Reachability
- An **ICMP Echo** message tests whether a host on an IP network is reachable.
- The local host sends an **Echo Request**. If the destination is available, it answers with an **Echo Reply**.

### Destination or Service Unreachable
- An ICMP **Destination Unreachable** message tells the source that a destination or service can't be reached.
- The message carries a **code** that says why the packet couldn't be delivered.

| Code | ICMPv4 | ICMPv6 |
|------|--------|--------|
| 0 | Net unreachable | No route to destination |
| 1 | Host unreachable | Communication with the destination is administratively prohibited (e.g., firewall) |
| 2 | Protocol unreachable | Beyond scope of the source address |
| 3 | Port unreachable | Address unreachable |
| 4 | - | Port unreachable |

### Time Exceeded
- **IPv4:** when the **TTL** field of a packet is decremented to 0, an ICMPv4 Time Exceeded message is sent to the source host.
- **IPv6:** ICMPv6 also sends Time Exceeded, but uses the IPv6 **Hop Limit** field instead of TTL.
- Time Exceeded messages are what the **traceroute** tool relies on.

### ICMPv6 Specific Messages
ICMPv6 includes four new messages as part of **Neighbor Discovery Protocol (ND/NDP)** that ICMPv4 doesn't have.

**Between an IPv6 router and a device (dynamic address allocation)**
- **Router Solicitation (RS):** sent by an IPv6 device to find out how to get its IPv6 address information dynamically.
- **Router Advertisement (RA):** sent by IPv6 routers every 200 seconds or in response to an RS. Gives the prefix, prefix length, DNS address, and domain name. A host using SLAAC sets its default gateway to the **link-local address** of the router that sent the RA.

**Between IPv6 devices**
- **Neighbor Solicitation (NS):**
  - Used for **Duplicate Address Detection (DAD)**, with the device's own IPv6 address as the target.
  - Also sent to a solicited-node address with a known target IPv6 address, to find the destination's MAC address.
- **Neighbor Advertisement (NA):** sent in reply to an NS. During DAD it announces that an address is already in use; for address resolution it carries the Ethernet MAC address.
- **Redirect:** part of ICMPv6 ND, with a function similar to the ICMPv4 redirect message.

## 2. Ping and Traceroute Tests

### Ping: Test Connectivity
- `ping` works for both IPv4 and IPv6 and uses ICMP **echo request** and **echo reply** messages to test connectivity between hosts.
- The summary shows the success rate and the average round-trip time.
- If no reply arrives before the timeout, ping reports that no response was received.
- It's **common for the first ping to time out** when ARP or ND has to run before the echo request can be sent.

### Ping the Loopback
- Tests the internal IPv4 or IPv6 configuration on the local host.
- Ping `127.0.0.1` (IPv4) or `::1` (IPv6).
- A reply means IP is installed correctly on the host. An error means TCP/IP isn't working on the host.

### Ping the Default Gateway
- Tests whether the host can communicate on the **local network**.
- A successful ping means the host and the router interface serving as its default gateway are both working on the local network.
- If the gateway doesn't answer, ping another working host on the local network.

### Ping a Remote Host
- Tests whether the host can communicate across an **internetwork**.
- A successful ping confirms communication on the local network too.
- No reply could be caused by security restrictions that block ICMP.

| Test | What it checks |
|------|----------------|
| Ping loopback (`127.0.0.1` / `::1`) | IP is working on the host itself |
| Ping default gateway | Host and gateway work on the local network |
| Ping remote host | Communication across the internetwork |

### Traceroute: Test the Path
- `traceroute` (`tracert` on Windows) tests the path between two hosts and lists the **hops** reached along the way.
- Shows the round-trip time for each hop, and an asterisk (`*`) for a failed response. Useful for finding problem routers, or routers set not to reply.
- It uses the IPv4 **TTL** or IPv6 **Hop Limit** field in the Layer 3 header together with ICMP **Time Exceeded** messages.

**How it works**
1. The first message is sent with TTL = 1, so it times out at the first router.
2. That router replies with an ICMP Time Exceeded message.
3. Traceroute raises the TTL (2, 3, 4...) for each new round of messages, capturing the address of each hop as packets expire farther along the path.
4. This continues until the destination is reached or a maximum TTL is hit.

## Exam Reminders
- ICMP reports problems; it doesn't fix them.
- Ping = echo request + echo reply. Traceroute = TTL/Hop Limit + Time Exceeded.
- Ping order for troubleshooting: loopback, then default gateway, then remote host.
- The first ping often times out because ARP/ND is resolving the address.
- ICMPv6 adds RS, RA, NS, and NA (Neighbor Discovery). RA is sent every 200 seconds.
- No ping reply doesn't always mean a fault: a firewall may block ICMP.
