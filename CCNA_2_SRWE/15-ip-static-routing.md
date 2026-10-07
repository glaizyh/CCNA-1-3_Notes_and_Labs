# Module 15: IP Static Routing

## 1. Static Routes Overview

### Types of Static Routes

| Type | Description |
|------|-------------|
| **Standard static route** | Configured to reach a specific remote network |
| **Default static route** | Matches all packets; serves as the gateway of last resort |
| **Floating static route** | Gives a backup path to a primary static or dynamic route |
| **Summary static route** | Combines several static routes into a single network entry |

Static routes are configured in global configuration mode with `ip route` (IPv4) or `ipv6 route` (IPv6).

### Next-Hop Options

| Option | What you specify | Notes |
|--------|------------------|-------|
| **Next-hop route** | Only the next-hop IP address | |
| **Directly connected static route** | Only the router's exit interface | Recommended mainly for point-to-point serial interfaces |
| **Fully specified static route** | Both the next-hop IP address and the exit interface | Used on multi-access interfaces (such as Ethernet) to name the next hop explicitly |

### Command Syntax

**IPv4**
```
Router(config)# ip route network-address subnet-mask { ip-address | exit-intf [ip-address] } [distance]
```

**IPv6**
```
Router(config)# ipv6 route ipv6-prefix/prefix-length { ipv6-address | exit-intf [ipv6-address] } [distance]
```

## 2. Configure IP Static Routes

### IPv4 Static Routes

**Next-hop static route**
```
R1(config)# ip route 172.16.1.0 255.255.255.0 172.16.2.2
R1(config)# ip route 192.168.1.0 255.255.255.0 172.16.2.2
R1(config)# ip route 192.168.2.0 255.255.255.0 172.16.2.2
```

**Directly connected static route**
```
R1(config)# ip route 172.16.1.0 255.255.255.0 s0/1/0
R1(config)# ip route 192.168.1.0 255.255.255.0 s0/1/0
R1(config)# ip route 192.168.2.0 255.255.255.0 s0/1/0
```

**Fully specified static route**
```
R1(config)# ip route 172.16.1.0 255.255.255.0 GigabitEthernet 0/0/1 172.16.2.2
R1(config)# ip route 192.168.1.0 255.255.255.0 GigabitEthernet 0/0/1 172.16.2.2
R1(config)# ip route 192.168.2.0 255.255.255.0 GigabitEthernet 0/0/1 172.16.2.2
```

### IPv6 Static Routes

**Next-hop static route**
```
R1(config)# ipv6 unicast-routing
R1(config)# ipv6 route 2001:db8:acad:1::/64 2001:db8:acad:2::2
R1(config)# ipv6 route 2001:db8:cafe:1::/64 2001:db8:acad:2::2
R1(config)# ipv6 route 2001:db8:cafe:2::/64 2001:db8:acad:2::2
```

**Directly connected static route**
```
R1(config)# ipv6 route 2001:db8:acad:1::/64 s0/1/0
R1(config)# ipv6 route 2001:db8:cafe:1::/64 s0/1/0
R1(config)# ipv6 route 2001:db8:cafe:2::/64 s0/1/0
```

**Fully specified static route (required for a link-local next hop)**
- **Requirement:** IPv6 link-local addresses are only unique on one link and aren't in the IPv6 routing table. When a link-local address is the next hop, the route **must** be fully specified and include the exit interface.

```
R1(config)# ipv6 route 2001:db8:acad:1::/64 s0/1/0 fe80::2
```

### Verification Commands

| Command | Shows |
|---------|-------|
| `show ip route static` / `show ipv6 route static` | Only the static route entries in the routing table |
| `show ip route [network]` / `show ipv6 route [network]` | Details for one destination network |
| `show running-config \| section ip route` / `... \| section ipv6 route` | The configured static route statements |

## 3. Configure IP Default Static Routes

### Default Static Route Overview
- **Definition:** a static route that matches all packets not listed explicitly in the routing table.
- **Gateway of last resort:** the fallback path for traffic to unknown destinations.
- **Common placement:** edge routers connected to service providers, and **stub routers** (routers with only one upstream neighbor).

### Configuration and Representation

**IPv4 quad-zero route:** uses `0.0.0.0 0.0.0.0` (network and mask). The `/0` mask means no bits need to match.
```
R1(config)# ip route 0.0.0.0 0.0.0.0 172.16.2.2
```
In the routing table it appears with an asterisk, `S*`, as a candidate default route.

**IPv6 default route:** uses the `::/0` prefix length, where no bits need to match.
```
R1(config)# ipv6 route ::/0 2001:db8:acad:2::2
```

## 4. Configure Floating Static Routes

### Concept and Administrative Distance
- **Purpose:** a backup path in case a primary static or dynamic route fails.
- **Administrative distance (AD):** a standard static route has an AD of **1**.
- **Operation:** a floating static route is configured with a **higher AD** than the primary route. It stays out of the routing table (it "floats") until the primary route disappears.

### Configuration Example
```
! Primary default route (AD = 1 by default):
R1(config)# ip route 0.0.0.0 0.0.0.0 172.16.2.2
R1(config)# ipv6 route ::/0 2001:db8:acad:2::2

! Backup floating static route (configured with AD = 5):
R1(config)# ip route 0.0.0.0 0.0.0.0 10.10.10.2 5
R1(config)# ipv6 route ::/0 2001:db8:feed:10::2 5
```

**Failover behavior:** if the link to R2 goes down, R1 switches to the backup path and puts the AD 5 route into the routing table.

## 5. Configure Static Host Routes

### Host Route Overview
- **Definition:** a route to one specific IP address, using a 32-bit mask (`255.255.255.255` or `/32`) for IPv4, or a `/128` prefix length for IPv6.
- **Three sources:**
  1. **Installed automatically:** created as a local route (`L`) when an IP address is assigned to an interface.
  2. **Static host route:** configured manually to send traffic to a specific server or host.
  3. **Obtained dynamically:** learned through other methods or protocols.

### Configuration Examples

**IPv4 and IPv6 static host routes**
```
Branch(config)# ip route 209.165.200.238 255.255.255.255 198.51.100.2
Branch(config)# ipv6 route 2001:db8:acad:2::238/128 2001:db8:acad:1::2
```

**IPv6 static host route with a link-local next hop (fully specified)**
```
Branch(config)# no ipv6 route 2001:db8:acad:2::238/128 2001:db8:acad:1::2
Branch(config)# ipv6 route 2001:db8:acad:2::238/128 serial 0/1/0 fe80::2
```

## Exam Reminders
- Static route types: standard, default, floating, summary.
- Next-hop options: next-hop only, exit interface only (point-to-point), or both (fully specified, for Ethernet).
- A link-local IPv6 next hop **requires** the exit interface (fully specified route).
- Default routes: `ip route 0.0.0.0 0.0.0.0 next-hop` and `ipv6 route ::/0 next-hop`. They show as `S*`.
- Floating static route = a higher AD than the primary (static default AD = 1).
- Host routes use /32 (IPv4) or /128 (IPv6).
- Verify with `show ip route static` and `show running-config | section ip route`.
- IPv6 routing needs `ipv6 unicast-routing`.
