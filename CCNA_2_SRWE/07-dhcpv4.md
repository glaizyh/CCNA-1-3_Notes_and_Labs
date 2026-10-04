# Module 7: DHCPv4

## 1. DHCPv4 Concepts

### DHCPv4 Server and Client
- **Purpose:** DHCPv4 dynamically assigns IPv4 addresses and other network configuration information.
- **Benefit:** saves network administrators a lot of time, since desktop clients make up most of the nodes on a network.
- **Implementation options:**
  - A dedicated DHCPv4 server (scalable and easy to manage).
  - A Cisco router running Cisco IOS, in a small branch or SOHO location.

### Lease Mechanism
- Addresses are **leased** for an administratively defined period (typically 24 hours to a week or more).
- Clients must contact the server periodically to extend the lease before it expires.
- This makes sure devices that move or power off don't hold on to addresses they no longer need.
- When a lease expires, the address goes back into the pool to be reused.

### Steps to Obtain a Lease (DORA)

| Step | Message | Type | Description |
|------|---------|------|-------------|
| 1 | **DHCPDISCOVER** | Broadcast | Client looks for a DHCPv4 server |
| 2 | **DHCPOFFER** | Unicast | Server replies with an address offer |
| 3 | **DHCPREQUEST** | Broadcast | Client accepts the IPv4 offer |
| 4 | **DHCPACK** | Unicast | Server acknowledges the acceptance |

### Steps to Renew a Lease

| Step | Message | Type | Description |
|------|---------|------|-------------|
| 1 | **DHCPREQUEST** | Unicast | Before the lease expires, the client asks the original offering server directly. If there's no reply, the client broadcasts to reach other servers |
| 2 | **DHCPACK** | Unicast | The server verifies and returns an acknowledgment that extends the lease |

## 2. Configure a Cisco IOS DHCPv4 Server

### Configuration Steps

**Step 1: Exclude IPv4 addresses** (for routers, static servers, printers, etc.)
```
R1(config)# ip dhcp excluded-address 192.168.10.1 192.168.10.9
R1(config)# ip dhcp excluded-address 192.168.10.254
```

**Step 2: Define a DHCPv4 pool name**
```
R1(config)# ip dhcp pool LAN-POOL-1
```

**Step 3: Configure the pool parameters**
```
R1(dhcp-config)# network 192.168.10.0 255.255.255.0
R1(dhcp-config)# default-router 192.168.10.1
R1(dhcp-config)# dns-server 192.168.11.5
R1(dhcp-config)# domain-name example.com
R1(dhcp-config)# lease 1 0 0
```
The `lease` values are days, hours, and minutes, so `1 0 0` is 1 day.

### Verification Commands

| Command | Shows |
|---------|-------|
| `show running-config \| section dhcp` | The configured DHCPv4 commands |
| `show ip dhcp binding` | The list of IPv4 address to MAC address bindings |
| `show ip dhcp server statistics` | Counts of DHCPv4 messages sent and received |

### Enabling and Disabling the DHCPv4 Service
- The service is **enabled by default** on Cisco IOS.
- Disable it with `no service dhcp`.
- Enable it again with `service dhcp`.

## 3. DHCPv4 Relay

### The Problem and the Solution
- **Problem:** enterprise DHCP servers are often in a central location, and routers **don't forward DHCP broadcasts** across subnets by default.
- **Solution:** configure the router as a **DHCP relay agent**, which forwards the broadcasts as unicast packets to the central server.

### Configuration Example
```
R1(config)# interface g0/0/0
R1(config-if)# ip helper-address 192.168.11.6
```
Put the command on the interface that **receives** the clients' broadcasts.

### Verification
```
show ip interface [interface-id]
```
Confirms the helper address is active on the interface.

### UDP Services Relayed by Default
`ip helper-address` automatically forwards 8 UDP services:

| Port | Service |
|------|---------|
| 37 | Time |
| 49 | TACACS |
| 53 | DNS |
| 67 | DHCP/BOOTP server |
| 68 | DHCP/BOOTP client |
| 69 | TFTP |
| 137 | NetBIOS name service |
| 138 | NetBIOS datagram service |

## 4. Configure a DHCPv4 Client

### Cisco Router as a DHCPv4 Client
Used in SOHO or branch locations that connect to an ISP through a cable or DSL modem.

```
SOHO(config)# interface GigabitEthernet0/0/1
SOHO(config-if)# ip address dhcp
SOHO(config-if)# no shutdown
```

**Verification:** `show ip interface [interface-id]` confirms the interface is up and that its IP address came from DHCP.

### Home/Wireless Router as a Client
- A home router's WAN setting defaults to **Automatic Configuration - DHCP**, so it gets an IPv4 address from the ISP automatically.

## Exam Reminders
- DORA: Discover (broadcast), Offer (unicast), Request (broadcast), Acknowledgment (unicast).
- Lease renewal: the client sends a unicast DHCPREQUEST to the original server first.
- Exclude addresses with `ip dhcp excluded-address`; configure the pool with `network`, `default-router`, `dns-server`, `domain-name`, and `lease`.
- `show ip dhcp binding` = which client got which address.
- Routers don't forward broadcasts, so use `ip helper-address` as a relay.
- `ip helper-address` relays 8 UDP services by default (including DNS, DHCP, and TFTP).
- A router gets its address from DHCP with `ip address dhcp`.
