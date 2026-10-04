# Module 3: VLANs

## 1. Overview of VLANs

### VLAN Definitions and Characteristics
- **VLANs (Virtual LANs)** are logical groupings of similar devices.
- They **segment** groups of devices that share the same switches, and make the network easier to organize.
- **Isolation:** broadcasts, multicasts, and unicasts stay inside their own VLAN.
- Each VLAN has its own unique range of IP addresses.
- VLANs create **smaller broadcast domains**.

### Benefits of VLAN Design

| Benefit | Description |
|---------|-------------|
| **Smaller broadcast domains** | Reduces the size of broadcast domains across the LAN |
| **Improved security** | Devices can only communicate with other devices in the same VLAN |
| **Improved IT efficiency** | Groups devices with similar requirements (e.g., faculty vs. students) |
| **Reduced cost** | One switch can support multiple groups or VLANs |
| **Better performance** | Smaller broadcast domains cut unnecessary traffic and improve bandwidth |
| **Simpler management** | Groups with common roles need similar resources and applications |

### Types of VLANs

**Default VLAN**
- **VLAN 1** is the default VLAN, the default native VLAN, and the default management VLAN.
- It can't be deleted or renamed.
- Cisco recommends moving those default roles to other VLANs.

**Other VLAN types**

| Type | Description |
|------|-------------|
| **Data VLAN** | Carries user-generated traffic (email, web). VLAN 1 is the default data VLAN, because all switch interfaces belong to it at first |
| **Native VLAN** | Used only for trunk links. On an 802.1Q trunk, all frames are tagged **except** those in the native VLAN |
| **Management VLAN** | Used for SSH/Telnet VTY traffic and should not carry end-user traffic. Typically configured as the SVI of a Layer 2 switch |
| **Voice VLAN** | Voice needs its own VLAN because of real-time requirements (see below) |

**Voice VLAN requirements**
- Assured bandwidth, high QoS priority, and congestion avoidance.
- Source-to-destination delay under **150 ms**.
- The switch port sends **CDP** frames to tell the IP phone about the voice VLAN, and forwards frames on the designated voice VLAN (e.g., VLAN 150).

## 2. VLANs in a Multi-Switched Environment

### VLAN Trunks
- A **trunk** is a point-to-point link between two network devices.
- It carries traffic for more than one VLAN and extends VLANs across the network.
- A trunk supports all VLANs by default and uses **802.1Q**.

### Frame Tagging and IEEE 802.1Q
- Without VLANs, switches flood broadcast, multicast, and unknown unicast traffic out all ports.
- With VLANs, traffic stays inside its VLAN. Communication **between** VLANs needs a **Layer 3 device**.
- **802.1Q** inserts a **4-byte tag** into the Ethernet frame header. The FCS must be recalculated when the tag is inserted, and again when it is removed before the frame goes to an end device.

**802.1Q tag fields**

| Field | Size | Purpose |
|-------|------|---------|
| Type (TPID) | 2 bytes | Set to hexadecimal `0x8100` |
| User priority | 3 bits | Supports QoS |
| Canonical Format Identifier (CFI) | 1 bit | Supports Token Ring frames on Ethernet |
| VLAN ID (VID) | 12 bits | Supports up to 4,096 VLAN values |

### Native VLANs and Voice Tagging
- Both ends of a trunk must use the **same native VLAN**.
- Each trunk is configured independently.
- A **VoIP phone** acts as a 3-port switch:
  - CDP tells the phone which voice VLAN to use.
  - The phone tags its own voice traffic with a Layer 2 Class of Service (CoS) priority value.
  - Traffic from a connected PC (the access VLAN) can be tagged or untagged.

## 3. VLAN Configuration

### VLAN Ranges (Catalyst 2960 / 3650)

| Range | VLANs | Notes |
|-------|-------|-------|
| **Normal** | 1 - 1005 | Used in small to medium businesses. Stored in **vlan.dat** in flash. VLANs 1 and 1002-1005 are created automatically and can't be deleted. VLANs 1002-1005 are reserved for legacy types (FDDI, Token Ring). VTP can synchronize them between switches |
| **Extended** | 1006 - 4094 | Used by service providers. Stored in **running-config**. Supports fewer VLAN features and requires VTP configuration |

### Creating and Assigning VLANs

**Create a VLAN**
```
S1# configure terminal
S1(config)# vlan 20
S1(config-vlan)# name student
S1(config-vlan)# end
```
An unnamed VLAN gets a default name such as `vlan0020`.

**Assign an access port**
```
S1# configure terminal
S1(config)# interface fastethernet 0/18
S1(config-if)# switchport mode access
S1(config-if)# switchport access vlan 20
S1(config-if)# end
```

**Configure data and voice VLANs on one port**
```
S1(config)# vlan 20
S1(config-vlan)# name student
S1(config-vlan)# vlan 150
S1(config-vlan)# name VOICE
S1(config-vlan)# exit
S1(config)# interface fastethernet 0/18
S1(config-if)# switchport mode access
S1(config-if)# switchport access vlan 20
S1(config-if)# mls qos trust cos
S1(config-if)# switchport voice vlan 150
S1(config-if)# end
```

### Verification and Management Commands

| Command | Shows |
|---------|-------|
| `show vlan brief` | VLAN names, status, and ports |
| `show vlan id [vlan-id]` | Information about one VLAN ID |
| `show vlan name [vlan-name]` | Information about one VLAN name |
| `show vlan summary` | Overall VLAN counts |
| `show interfaces [interface-id] switchport` | Data, native, and voice VLAN assignments |

**Changing port membership**
- Re-enter `switchport access vlan [vlan-id]`.
- Use `no switchport access vlan` to put the interface back in VLAN 1.

**Deleting VLANs**
- One VLAN: `no vlan [vlan-id]` (reassign its ports first!).
- All VLANs: `delete flash:vlan.dat` (or `delete vlan.dat`).
- Full reset: unplug the data cables, erase startup-config, delete `vlan.dat`, and reload.

## 4. VLAN Trunks

### Trunk Configuration
```
S1(config)# interface fastethernet 0/1
S1(config-if)# switchport mode trunk
S1(config-if)# switchport trunk native vlan 99
S1(config-if)# switchport trunk allowed vlan 10,20,30,99
S1(config-if)# end
```
Layer 3 switches need the trunk encapsulation configured before trunk mode can be enabled.

### Resetting Trunk Settings
- Reset to default trunk settings: `no switchport trunk allowed vlan` and `no switchport trunk native vlan` (the native VLAN goes back to VLAN 1).
- Put the port back in access mode: `switchport mode access`.

## 5. Dynamic Trunking Protocol (DTP)

### DTP Overview
- **DTP** is a Cisco proprietary protocol that negotiates trunking.
- Enabled by default on Catalyst 2960 and 2950 switches.
- The default mode on 2960/2950 interfaces is `dynamic auto`.
- Use `switchport nonegotiate` on static trunk or access ports to turn DTP off and avoid negotiation problems.

### DTP Modes

| Mode | Behavior |
|------|----------|
| **access** | Permanent access mode; tries to turn the neighbor link into access |
| **dynamic auto** | Becomes a trunk if the neighbor is set to trunk or desirable |
| **dynamic desirable** | Actively tries to become a trunk by negotiating with auto or desirable interfaces |
| **trunk** | Permanent trunk mode; tries to turn the neighbor link into a trunk |

### DTP Results Matrix

| | Dynamic Auto | Dynamic Desirable | Trunk | Access |
|--|--------------|-------------------|-------|--------|
| **Dynamic Auto** | Access | Trunk | Trunk | Access |
| **Dynamic Desirable** | Trunk | Trunk | Trunk | Access |
| **Trunk** | Trunk | Trunk | Trunk | Limited connectivity |
| **Access** | Access | Access | Limited connectivity | Access |

### Verification
```
show dtp interface [interface-id]
```
Shows the current DTP status and mode of an interface.

## Exam Reminders
- VLAN 1 is the default, native, and management VLAN, and it can't be deleted.
- 802.1Q adds a 4-byte tag (TPID 0x8100, 12-bit VID). The native VLAN is not tagged.
- Normal range VLANs 1-1005 go in `vlan.dat`; extended range 1006-4094 go in running-config.
- Voice VLAN delay must be under 150 ms; use `switchport voice vlan` and `mls qos trust cos`.
- Both ends of a trunk must use the same native VLAN.
- Two `dynamic auto` ports end up as access; auto + desirable = trunk.
- Delete a VLAN only after reassigning its ports; use `delete flash:vlan.dat` to wipe all.
