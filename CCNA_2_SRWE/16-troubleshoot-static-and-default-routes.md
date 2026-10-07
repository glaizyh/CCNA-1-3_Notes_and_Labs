# Module 16: Troubleshoot Static and Default Routes

## 1. Packet Processing with Static Routes

### Static Routes and Packet Forwarding
1. **Default gateway forwarding:** a host addresses a packet to another host and sends it to its default gateway.
2. **Decapsulation and lookup:** when the packet arrives on a router interface, the router decapsulates it and searches its routing table for a matching destination network.
3. **Routing decision:**

| Result | What happens |
|--------|--------------|
| **Static route match** | The router uses the static route to find the next-hop IP address or exit interface |
| **Default route match** | If there is no specific route to the destination network, the router uses the default static route (if one is configured) |
| **No match** | The router drops the packet and sends an ICMP message back to the source host |

4. **Forwarding:** if there is a match, the router encapsulates the packet in a new frame and forwards it out the exit interface toward the destination.

### Destination Network Processing (Layer 2 Resolution)
1. **Directly connected check:** when a packet reaches the router on the destination network, the router decapsulates it and searches the routing table.
2. **ARP lookup:** if the destination IP address matches a directly connected Ethernet interface, the router looks in its ARP table for the MAC address of that IP address.
3. **ARP resolution:**
   - If there is no ARP entry, the router **broadcasts an ARP request** out the interface.
   - The destination host answers with an **ARP reply** containing its MAC address.
4. **Final delivery:** the router encapsulates the packet in a new frame, with the destination host's MAC address as the destination MAC and the router interface's MAC address as the source MAC, and sends it to the host's NIC.

## 2. Troubleshoot IPv4 Static and Default Route Configuration

### Network Failure Causes
- **Common causes:** interface failures, service provider outages, oversaturated links, or wrong configurations entered by an administrator.
- **Administrator's role:** find and fix problems efficiently, using diagnostic tools to isolate routing problems quickly.

### Common Troubleshooting Commands

| Command | Description |
|---------|-------------|
| `ping` | Verifies Layer 3 connectivity to a destination. Extended pings give extra diagnostic options |
| `traceroute` | Verifies the path to a destination network by finding each intermediate hop (each hop answers with an ICMP Time Exceeded message) |
| `show ip route` | Shows the routing table, to check the route entries for destination IP addresses |
| `show ip interface brief` | Shows interface status, to check that interfaces are up and have the right IP addresses |
| `show cdp neighbors` / `show cdp neighbors detail` | Shows directly connected Cisco devices, to confirm Layer 1 and Layer 2 connectivity |

### Solving Connectivity Problems
- **Isolating the failure:** use step-by-step pings and extended pings to find where packets are being dropped along the path.
- **Routing table analysis:** run `show ip route` on the intermediate routers to look for wrong next-hop IP addresses or missing entries.
- **Corrective action:**
  - Remove an incorrect static route: `no ip route [network] [mask] [next-hop/interface]`
  - Add the static route again with the correct next-hop IP address or exit interface to restore connectivity.

```
R1(config)# no ip route 192.168.2.0 255.255.255.0 172.16.2.3
R1(config)# ip route 192.168.2.0 255.255.255.0 172.16.2.2
```

## Exam Reminders
- Routers look up the destination in the routing table (longest match); no match and no default route = drop the packet and send ICMP back.
- On the destination network, the router uses ARP to find the host's MAC address before delivering the frame.
- Troubleshooting order: `ping`, `traceroute`, `show ip route`, `show ip interface brief`, `show cdp neighbors`.
- To fix a wrong static route, remove it with `no ip route ...`, then add the correct one.
- Check both directions: a working route out also needs a working route back.
