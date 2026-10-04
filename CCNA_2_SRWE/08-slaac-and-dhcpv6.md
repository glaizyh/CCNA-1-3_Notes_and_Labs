# Module 8: SLAAC and DHCPv6

## 1. IPv6 Global Unicast Address (GUA) Assignment

### Overview and Dynamic Options
- **Static assignment:** configured manually with `ipv6 address ipv6-address/prefix-length`. Slow and error-prone on hosts.
- **Dynamic assignment:** hosts use ICMPv6 **Router Advertisement (RA)** messages, sent every 200 seconds or in reply to a **Router Solicitation (RS)**.
- **Link-local address (LLA):** created automatically at boot, once the Ethernet interface is active. Used as the default gateway.

### RA Message Flags
Three flags in the ICMPv6 RA message decide how a host gets its GUA:
- **A flag (Address Autoconfiguration):** tells the host to use **SLAAC** to build its own GUA.
- **O flag (Other Configuration):** tells the host to get extra parameters (DNS, domain name) from a **stateless DHCPv6** server.
- **M flag (Managed Address Configuration):** tells the host to get all addressing information from a **stateful DHCPv6** server.

| Method | A | O | M | Description |
|--------|---|---|---|-------------|
| **SLAAC only** (default) | 1 | 0 | 0 | The host uses the RA's prefix and prefix length and generates its own interface ID |
| **Stateless DHCPv6** | 1 | 1 | 0 | The host builds its GUA with SLAAC and gets DNS and other options from a DHCPv6 server |
| **Stateful DHCPv6** | 0 | 0 | 1 | The host gets its full IP configuration from a stateful DHCPv6 server |

## 2. SLAAC Operation

### SLAAC Overview
- **Stateless:** no server tracks address allocations or usage.
- **Enable SLAAC** with the global command `ipv6 unicast-routing`. It makes the router join the all-routers multicast group (`FF02::2`) and send RAs to the all-nodes group (`FF02::1`).

### Interface ID Generation and DAD

**How the host builds its interface ID**
- **Randomly generated:** the default on modern operating systems such as Windows 10.
- **EUI-64:** takes the 48-bit Ethernet MAC address and inserts `FF:FE` in the middle.

**Duplicate Address Detection (DAD)**
- The host sends an ICMPv6 **Neighbor Solicitation (NS)** to its own solicited-node multicast address.
- If no **Neighbor Advertisement (NA)** comes back, the address is unique and is bound to the interface.

## 3. DHCPv6 Overview and Operation

### Message Exchange Process
DHCPv6 uses UDP destination **port 547** (client to server) and **port 546** (server to client).

| Step | Message | Description |
|------|---------|-------------|
| 1 | **RS** (Router Solicitation) | The host sends it to `FF02::2` |
| 2 | **RA** (Router Advertisement) | The router sends it to `FF02::1`, with the flags that say how to get an address |
| 3 | **SOLICIT** | The host sends it to `FF02::1:2` (all DHCPv6 servers; an IPv6 multicast, not a broadcast) |
| 4 | **ADVERTISE** | A unicast reply from the available DHCPv6 server(s) |
| 5 | **REQUEST / INFORMATION-REQUEST** | The host asks for addressing (stateful) or options (stateless) by unicast |
| 6 | **REPLY** | The server confirms the configuration by unicast |

## 4. DHCPv6 Configuration

### Stateless DHCPv6 Server
```
R1(config)# ipv6 unicast-routing
R1(config)# ipv6 dhcp pool IPV6-STATELESS
R1(config-dhcp)# dns-server 2001:db8:acad:1::254
R1(config-dhcp)# domain-name example.com
R1(config-dhcp)# exit
R1(config)# interface gigabitethernet 0/0/1
R1(config-if)# ipv6 dhcp server IPV6-STATELESS
R1(config-if)# ipv6 nd other-config-flag
R1(config-if)# end
```

### Stateful DHCPv6 Server
```
R1(config)# ipv6 unicast-routing
R1(config)# ipv6 dhcp pool IPV6-STATEFUL
R1(config-dhcp)# address prefix 2001:db8:acad:1::/64
R1(config-dhcp)# dns-server 2001:db8:acad:1::254
R1(config-dhcp)# domain-name example.com
R1(config-dhcp)# exit
R1(config)# interface gigabitethernet 0/0/1
R1(config-if)# ipv6 dhcp server IPV6-STATEFUL
R1(config-if)# ipv6 nd managed-config-flag
R1(config-if)# ipv6 nd prefix default no-autoconfig
R1(config-if)# end
```

### Router as a DHCPv6 Client

**Stateless client**
```
Client(config)# ipv6 unicast-routing
Client(config)# interface gigabitethernet 0/0/1
Client(config-if)# ipv6 enable
Client(config-if)# ipv6 address autoconfig
```

**Stateful client**
```
Client(config)# ipv6 unicast-routing
Client(config)# interface gigabitethernet 0/0/1
Client(config-if)# ipv6 enable
Client(config-if)# ipv6 address dhcp
```

### DHCPv6 Relay Agent
Configured on the **client-facing** interface when the DHCPv6 server is on a different subnet.

```
R1(config)# interface gigabitethernet 0/0/1
R1(config-if)# ipv6 dhcp relay destination 2001:db8:acad:1::2 gigabitethernet 0/0/0
R1(config-if)# end
```

## 5. Verification Commands

| Command | Shows |
|---------|-------|
| `show ipv6 interface [interface-id]` | LLA, GUA, joined multicast groups, and ND flag settings |
| `show ipv6 dhcp pool` | Pool options and the number of active clients |
| `show ipv6 dhcp binding` | Client LLAs, assigned GUAs, and lease times (stateful only) |
| `show ipv6 dhcp interface` | Interface binding, client status, or relay destination mode |

## Exam Reminders
- RA flags: **SLAAC** = A1/O0/M0; **stateless DHCPv6** = A1/O1/M0; **stateful DHCPv6** = A0/O0/M1.
- SLAAC needs `ipv6 unicast-routing` on the router so it sends RAs.
- Stateless: `ipv6 nd other-config-flag`. Stateful: `ipv6 nd managed-config-flag` and `ipv6 nd prefix default no-autoconfig`.
- Interface ID: random (modern Windows) or EUI-64 (insert FF:FE).
- DAD uses NS to the host's own solicited-node address; no reply means the address is unique.
- DHCPv6 ports: client to server 547, server to client 546. SOLICIT goes to `FF02::1:2`.
- Stateless client: `ipv6 address autoconfig`. Stateful client: `ipv6 address dhcp`.
- `show ipv6 dhcp binding` only shows leases in stateful mode.
