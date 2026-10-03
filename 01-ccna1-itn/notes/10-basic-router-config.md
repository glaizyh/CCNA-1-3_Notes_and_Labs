# Module 10: Basic Router Configuration

## 1. Configure Initial Router Settings

### Basic Router Configuration Steps

| Task | Commands |
|------|----------|
| Set the device name | `Router(config)# hostname hostname` |
| Secure privileged EXEC mode | `Router(config)# enable secret password` |
| Secure user EXEC mode (console) | `Router(config)# line console 0`<br>`Router(config-line)# password password`<br>`Router(config-line)# login` |
| Secure remote access (Telnet/SSH) | `Router(config)# line vty 0 4`<br>`Router(config-line)# password password`<br>`Router(config-line)# login`<br>`Router(config-line)# transport input {ssh \| telnet}` |
| Encrypt plaintext passwords | `Router(config)# service password-encryption` |
| Legal banner | `Router(config)# banner motd # message #` |
| Save the configuration | `Router(config)# end`<br>`Router# copy running-config startup-config` |

### Basic Router Configuration Example

```
R1(config)# hostname R1
R1(config)# enable secret class
R1(config)# line console 0
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# line vty 0 4
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# transport input ssh telnet
R1(config-line)# exit
R1(config)# service password-encryption
R1(config)# banner motd # WARNING: Unauthorized access is prohibited! #
R1(config)# exit
R1# copy running-config startup-config
```

## 2. Configure Interfaces

### Configure Router Interfaces
- To make a router reachable, its interfaces must be configured and activated.
- Good practice: use the `description` command to note what network is connected to the interface.
- The `no shutdown` command **activates** the interface.

**Interface configuration syntax**
```
Router(config)# interface type-and-number
Router(config-if)# description description-text
Router(config-if)# ip address ipv4-address subnet-mask
Router(config-if)# ipv6 address ipv6-address/prefix-length
Router(config-if)# no shutdown
```

### Router Interface Configuration Examples

**LAN interface (G0/0/0)**
```
R1(config)# interface gigabitEthernet 0/0/0
R1(config-if)# description Link to LAN
R1(config-if)# ip address 192.168.10.1 255.255.255.0
R1(config-if)# ipv6 address 2001:db8:acad:10::1/64
R1(config-if)# no shutdown
R1(config-if)# exit
```

**Router-to-router interface (G0/0/1)**
```
R1(config)# interface gigabitEthernet 0/0/1
R1(config-if)# description Link to R2
R1(config-if)# ip address 209.165.200.225 255.255.255.252
R1(config-if)# ipv6 address 2001:db8:feed:224::1/64
R1(config-if)# no shutdown
R1(config-if)# exit
```

### Interface Verification Commands

| Command | Description |
|---------|-------------|
| `show ip interface brief`<br>`show ipv6 interface brief` | All interfaces, their IP addresses, and their current status |
| `show ip route`<br>`show ipv6 route` | The contents of the IP routing tables stored in RAM |
| `show interfaces` | Statistics for all interfaces on the device (only shows IPv4 addressing information) |
| `show ip interface` | IPv4 statistics for all interfaces on a router |
| `show ipv6 interface` | IPv6 statistics for all interfaces on a router |

## 3. Configure the Default Gateway

### Default Gateway on a Host
- **Purpose:** used when a host sends a packet to a device on another network.
- **Address:** generally the router interface address attached to the host's local network.
- **Rule:** the host's IP address and the router interface address must be in the **same network**.
- **Example:** to reach PC3 from PC1, PC1 addresses the packet with PC3's IPv4 address but forwards it to its default gateway (interface G0/0/0 of R1).

### Default Gateway on a Switch
- A switch needs a default gateway configured so it can be managed remotely from another network.

```
Switch(config)# ip default-gateway ip-address
```

## Exam Reminders
- Router interfaces are **off by default**; always add `no shutdown`.
- `enable secret` is stored hashed; line passwords are plaintext unless `service password-encryption` is on.
- `show ip interface brief` is the fastest way to check status and IP addresses.
- A host's default gateway must be on the **same network** as the host.
- A switch uses `ip default-gateway`; a router uses routes.
- Save with `copy running-config startup-config`.
