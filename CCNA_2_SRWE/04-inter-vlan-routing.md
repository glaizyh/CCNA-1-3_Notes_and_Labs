# Module 4: Inter-VLAN Routing

## 1. Inter-VLAN Routing Operation

### What Is Inter-VLAN Routing?
- **Inter-VLAN routing** is forwarding network traffic from one VLAN to another VLAN.
- Hosts in one VLAN can't communicate with hosts in another VLAN unless a **router or a Layer 3 switch** provides routing services.

### Three Inter-VLAN Routing Options

| Option | How it works | Limitation / notes |
|--------|--------------|--------------------|
| **Legacy inter-VLAN routing** | A router with multiple physical Ethernet interfaces, each connected to a switch port in a different VLAN. Each router interface is the default gateway for its VLAN subnet | Not scalable: routers have few physical interfaces. No longer used in switched networks |
| **Router-on-a-stick** | One physical Ethernet interface routes between multiple VLANs, using software-based virtual interfaces called **subinterfaces**, each with its own IP address and VLAN assignment | Fine for small to medium networks. Doesn't scale beyond about 50 VLANs |
| **Layer 3 switch with SVIs** | **Switched Virtual Interfaces (SVIs)** configured directly on a Layer 3 (multilayer) switch | The most scalable option, for medium to large organizations |

### Layer 3 Switches: Advantages and Disadvantages

**Advantages**
- Much faster than router-on-a-stick, because everything is switched and routed in hardware.
- No external physical links between the switch and a router are needed for routing.
- Bandwidth can be increased with Layer 2 EtherChannels as trunk links between switches.
- Much lower latency, because data doesn't leave the switch to be routed.
- More common than routers in campus LANs.

**Disadvantage**
- Layer 3 switches are more expensive.

## 2. Router-on-a-Stick Inter-VLAN Routing

### Subinterface Overview and Commands
- A **subinterface** is a software-based virtual interface tied to one physical Ethernet interface.
- Create it with: `interface interface_id.subinterface_id`
- It is customary to match the subinterface number to the VLAN number.
- Each subinterface needs two main commands:

| Command | Purpose |
|---------|---------|
| `encapsulation dot1q vlan_id [native]` | Makes the subinterface respond to 802.1Q traffic for that VLAN ID. Add `native` only to set the native VLAN to something other than VLAN 1 |
| `ip address ip-address subnet-mask` | Sets the subinterface's IPv4 address, which usually serves as the default gateway for that VLAN |

- **Enable the physical interface** with `no shutdown`. If the physical interface is disabled, all its subinterfaces are disabled.

### Subinterface Configuration Example
```
R1# configure terminal
R1(config)# interface gigabitethernet 0/0/1.10
R1(config-subif)# description Default Gateway for VLAN 10
R1(config-subif)# encapsulation dot1q 10
R1(config-subif)# ip address 192.168.10.1 255.255.255.0
R1(config-subif)# exit
R1(config)# interface gigabitethernet 0/0/1.20
R1(config-subif)# description Default Gateway for VLAN 20
R1(config-subif)# encapsulation dot1q 20
R1(config-subif)# ip address 192.168.20.1 255.255.255.0
R1(config-subif)# exit
R1(config)# interface gigabitethernet 0/0/1.99
R1(config-subif)# description Default Gateway for VLAN 99
R1(config-subif)# encapsulation dot1q 99
R1(config-subif)# ip address 192.168.99.1 255.255.255.0
R1(config-subif)# exit
R1(config)# interface gigabitethernet 0/0/1
R1(config-if)# description Trunk link to S1
R1(config-if)# no shutdown
R1(config-if)# end
```

### Verification Commands

| Command | Verifies |
|---------|----------|
| `ping destination-ip` | Connectivity from a host to a host in another VLAN |
| `ipconfig` | Current IP configuration of a Windows host |
| `show ip route` | Routing table entries on the router |
| `show ip interface brief` | Status of router interfaces and subinterfaces |
| `show interfaces` | Detailed interface and encapsulation status |
| `show interfaces trunk` | Trunk links on the connected switches |

## 3. Inter-VLAN Routing Using Layer 3 Switches

### Layer 3 Switch Capabilities
- Route from one VLAN to another using multiple **SVIs**.
- Convert a Layer 2 switch port into a Layer 3 interface (a **routed port**), similar to a physical interface on a Cisco IOS router.

### Layer 3 SVI Configuration
```
D1# configure terminal
D1(config)# vlan 10
D1(config-vlan)# name LAN10
D1(config-vlan)# vlan 20
D1(config-vlan)# name LAN20
D1(config-vlan)# exit
D1(config)# interface vlan 10
D1(config-if)# ip address 192.168.10.1 255.255.255.0
D1(config-if)# exit
D1(config)# interface vlan 20
D1(config-if)# ip address 192.168.20.1 255.255.255.0
D1(config-if)# exit
D1(config)# interface gigabitethernet 1/0/6
D1(config-if)# switchport mode access
D1(config-if)# switchport access vlan 10
D1(config-if)# exit
D1(config)# interface gigabitethernet 1/0/18
D1(config-if)# switchport mode access
D1(config-if)# switchport access vlan 20
D1(config-if)# exit
D1(config)# ip routing
D1(config)# end
```

**Important:** `ip routing` must be configured to enable inter-VLAN routing for IPv4 on a Layer 3 switch.

### Routed Ports on a Layer 3 Switch
- A **routed port** is made by turning off the switchport feature on a Layer 2 port with `no switchport`.
- It can then take an IPv4 configuration, to connect to a router or another Layer 3 switch.

```
D1# configure terminal
D1(config)# interface gigabitethernet 1/0/1
D1(config-if)# no switchport
D1(config-if)# ip address 10.10.10.2 255.255.255.0
D1(config-if)# no shutdown
D1(config-if)# exit
D1(config)# ip routing
D1(config)# end
```

## 4. Troubleshoot Inter-VLAN Routing

### Common Inter-VLAN Issues and Fixes

| Issue | How to fix | How to verify |
|-------|-----------|---------------|
| **Missing VLANs** | Create (or re-create) the VLAN if it doesn't exist. Make sure the host port is in the correct VLAN. Make sure trunks are configured correctly | `show vlan [brief]`, `show interfaces switchport`, `ping` |
| **Switch trunk port issues** | Make sure the port is a trunk port and is enabled | `show interface trunk`, `show running-config` |
| **Switch access port issues** | Assign the correct VLAN to the access port. Make sure the port is an access port and is enabled | `show interfaces switchport`, `show running-config interface` |
| **Router configuration issues** | Correct the subinterface IPv4 address. Correct the host's subnet configuration. Make sure the subinterface is assigned to the correct VLAN ID | `ipconfig`, `show ip interface brief`, `show interfaces` |

### Useful Filter for Troubleshooting
`show interfaces` on a router prints a lot of output. Cut it down with the `include` keyword:

```
R1# show interfaces | include Gig|802.1Q
```

## Exam Reminders
- Three options: legacy (one router port per VLAN), router-on-a-stick (subinterfaces), Layer 3 switch (SVIs).
- Router-on-a-stick: match the subinterface number to the VLAN, use `encapsulation dot1q <vlan>`, and `no shutdown` on the physical interface.
- The switch port connected to the router in router-on-a-stick must be a **trunk**.
- Layer 3 switch: SVIs plus `ip routing`. Routed port = `no switchport` plus an IP address.
- Layer 3 switches are faster and more scalable but cost more.
- Troubleshooting order: VLANs exist, trunks and access ports are correct, router subinterfaces and host addressing are correct.
