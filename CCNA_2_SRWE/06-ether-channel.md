# Module 6: EtherChannel

## 1. EtherChannel Operation

### Link Aggregation Overview
- **EtherChannel** is a link aggregation technology that groups several physical Ethernet links into **one logical link**.
- **Purpose:** fault tolerance, load sharing, more bandwidth, and redundancy between switches, routers, and servers.
- **STP interoperability:** STP sees the whole EtherChannel bundle as a single logical link, so it does **not** block the redundant physical links inside the channel.
- **Port channel:** the virtual interface created when you configure an EtherChannel.

### Advantages of EtherChannel
- **Configuration consistency:** most settings are applied on the port channel interface, which applies them to all member ports.
- **Cost-effective:** uses existing switch ports, with no need for expensive upgrades to faster single links.
- **Load balancing:** spreads traffic across all active links in the bundle.
- **Seamless redundancy:** if one physical link fails, traffic moves to the remaining links without causing an STP topology change.

### Implementation Restrictions
- **Interface types can't be mixed** (for example, Fast Ethernet and Gigabit Ethernet can't be in the same bundle).
- **Port limit and bandwidth:** up to **8 compatibly configured Ethernet ports** per EtherChannel, giving full-duplex bandwidth of up to 800 Mbps (Fast EtherChannel) or 8 Gbps (Gigabit EtherChannel).
- **Switch support:** a Catalyst 2960 supports up to **6** EtherChannels.
- **Consistency:** all member ports must have identical settings (speed, duplex mode, Layer 2 mode, access/trunk settings, and native and allowed VLANs).

## 2. Auto-Negotiation Protocols
EtherChannels can be formed **statically** (without negotiation) or **dynamically** with a negotiation protocol.

### Port Aggregation Protocol (PAgP)
- **Cisco-proprietary.**
- Sends PAgP packets every **30 seconds** to check configuration consistency and manage link additions and failures.

| PAgP mode | Behavior |
|-----------|----------|
| **On** | Forces the interface to channel without PAgP packets. Works only if the other side is also set to On |
| **Desirable** | Active negotiation: starts negotiation by sending PAgP packets |
| **Auto** | Passive negotiation: replies to PAgP packets it receives but doesn't start negotiation |

**PAgP mode combinations**

| Switch 1 | Switch 2 | Channel established? |
|----------|----------|----------------------|
| On | On | Yes |
| On | Desirable / Auto | No |
| Desirable | Desirable | Yes |
| Desirable | Auto | Yes |
| Auto | Desirable | Yes |
| Auto | Auto | No |

### Link Aggregation Control Protocol (LACP)
- **IEEE standard (802.3ad)**, so it works across vendors.

| LACP mode | Behavior |
|-----------|----------|
| **On** | Forces the interface to channel without LACP packets |
| **Active** | Active negotiation: starts negotiation by sending LACP packets |
| **Passive** | Passive negotiation: replies to LACP packets but doesn't start negotiation |

**LACP mode combinations**

| Switch 1 | Switch 2 | Channel established? |
|----------|----------|----------------------|
| On | On | Yes |
| On | Active / Passive | No |
| Active | Active | Yes |
| Active | Passive | Yes |
| Passive | Active | Yes |
| Passive | Passive | No |

## 3. EtherChannel Configuration

### Configuration Guidelines
- All physical interfaces must support EtherChannel (they don't need to be physically next to each other).
- Speed, duplex, access/trunk mode, native VLAN, and the allowed VLAN range must **match** on all bundled ports.
- A port channel can be an access port, a trunk port (most common), or a routed port.

### LACP Configuration Steps
```
S1# configure terminal
S1(config)# interface range FastEthernet 0/1 - 2
S1(config-if-range)# channel-group 1 mode active
Creating a port-channel interface Port-channel 1
S1(config-if-range)# exit
S1(config)# interface port-channel 1
S1(config-if)# switchport mode trunk
S1(config-if)# switchport trunk allowed vlan 1,2,20
S1(config-if)# end
```

## 4. Verification and Troubleshooting

### Verification Commands

| Command | Shows |
|---------|-------|
| `show interfaces port-channel` | General status of the logical port channel interface |
| `show etherchannel summary` | A one-line summary per port channel (status, protocol, member ports) |
| `show etherchannel port-channel` | Detailed information about one port channel interface |
| `show interfaces etherchannel` | The role of each physical member interface |

### Common Configuration Issues
- **Mismatched VLANs:** member ports belong to different access VLANs or native VLANs.
- **Mismatched trunking:** trunk mode is set on some member ports but not all.
- **Allowed VLAN mismatch:** the allowed VLAN range differs across the trunk ports.
- **Protocol mismatch:** incompatible PAgP or LACP modes on the two ends.

### Correcting Misconfigurations
When you change EtherChannel settings, remove and re-create the port-channel interface, or adjust the member links carefully. Otherwise STP errors can put interfaces into a **blocking** or **err-disabled** state.

```
S1(config)# no interface port-channel 1
S1(config)# interface range fa0/1 - 2
S1(config-if-range)# channel-group 1 mode desirable
S1(config-if-range)# no shutdown
S1(config-if-range)# exit
S1(config)# interface port-channel 1
S1(config-if)# switchport mode trunk
S1(config-if)# end
```

## Exam Reminders
- EtherChannel = several physical links acting as one logical link; STP treats it as one link.
- Max 8 ports per bundle; don't mix Fast Ethernet and Gigabit Ethernet.
- PAgP = Cisco proprietary (on, desirable, auto). LACP = IEEE 802.3ad (on, active, passive).
- A channel forms if at least one side is **active/desirable** (or both are **on**). Auto + auto and passive + passive do **not** form one.
- `on` only works with `on` on the other side.
- All member ports must match in speed, duplex, VLAN, and trunk settings.
- Verify with `show etherchannel summary`.
