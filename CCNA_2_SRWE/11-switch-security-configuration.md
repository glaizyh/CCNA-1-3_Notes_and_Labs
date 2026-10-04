# Module 11: Switch Security Configuration

## 1. Implement Port Security

### Secure Unused Ports
- All switch ports should be secured **before** the switch goes into production.
- A simple and effective method is to **shut down unused ports** with `shutdown`. Use `no shutdown` to turn a port back on.
- Use `interface range` to configure several ports at once:

```
Switch(config)# interface range type module/first-number - last-number
```

### Mitigate MAC Address Table Attacks
- **Port security** stops MAC address table overflow attacks by limiting the valid MAC addresses allowed on a port.
- MAC addresses can be configured manually, or learned dynamically up to a limited number.
- Limiting a port to **1** MAC address controls unauthorized access.

### Enable and Configure Port Security
- **Prerequisite:** port security only works on ports manually set to **access** (or trunk). Ports default to `dynamic auto`, and port security is rejected until the port is set to access.

```
S1(config)# interface f0/1
S1(config-if)# switchport mode access
S1(config-if)# switchport port-security
```

### Limit and Learn MAC Addresses
- **Maximum addresses:** `switchport port-security maximum [value]`. The default is **1**; the upper limit depends on the switch and IOS (up to 8192).

| Learning method | Description |
|-----------------|-------------|
| **Manually configured** | Static entry with `switchport port-security mac-address [mac-address]` |
| **Dynamically learned** | The switch secures the currently connected MAC address, but loses it on reboot |
| **Dynamically learned, sticky** | The switch learns MAC addresses and "sticks" them to the running-config with `switchport port-security mac-address sticky`. Saving the configuration writes them to NVRAM |

### Port Security Aging
- **Purpose:** removes secure MAC addresses from a port without deleting them by hand.
- **Absolute:** secure addresses are deleted after the aging time passes.
- **Inactivity:** secure addresses are deleted only if they've been inactive for the aging time.

```
Switch(config-if)# switchport port-security aging {static | time mins | type {absolute | inactivity}}
```

### Security Violation Modes
Set the mode with `switchport port-security violation {shutdown | restrict | protect}`.

| Mode | Action on violation | Syslog message | Counter increases | How to recover |
|------|---------------------|----------------|-------------------|----------------|
| **shutdown** (default) | The port goes into the `err-disabled` state immediately and the LED turns off | Yes | Yes | `shutdown`, then `no shutdown` |
| **restrict** | Drops packets from unknown source MACs until the count falls below the maximum | Yes | Yes | Recovers automatically when the bad MAC stops |
| **protect** | Drops packets from unknown source MACs (the least secure mode) | No | No | Recovers automatically when the bad MAC stops |

### Verification Commands

| Command | Shows |
|---------|-------|
| `show port-security` | Port security settings across all switch interfaces |
| `show port-security interface [interface-id]` | Detailed status, violation mode, and violation count for one interface |
| `show port-security address` | All secure MAC addresses (static, dynamic, sticky) on the ports |
| `show running-config \| begin interface [interface-id]` | Whether dynamic MAC addresses have stuck to the configuration |

## 2. Mitigate VLAN Attacks

### VLAN Attack Types
- **DTP spoofing:** the attacking host sends fake DTP messages to force the switch into trunking mode and reach target VLANs.
- **Rogue switch:** an unauthorized switch with trunking enabled is added to reach all VLANs.
- **Double tagging:** a hidden inner 802.1Q tag inside an outer native VLAN tag sends one-way frames to target VLANs.

### Mitigation Steps for VLAN Hopping
1. Disable DTP negotiation on non-trunk ports: `switchport mode access`.
2. Disable unused ports and assign them to an unused VLAN.
3. Enable trunking manually on the links that need it: `switchport mode trunk`.
4. Disable DTP negotiation on trunk ports: `switchport nonegotiate`.
5. Set the native VLAN to something other than VLAN 1: `switchport trunk native vlan [vlan-number]`.

## 3. Mitigate DHCP Attacks

### DHCP Attack Types
- **DHCP starvation:** tools like Gobbler make fake source MAC addresses to use up all the addresses in the pool, causing a DoS. Mitigated by **port security**.
- **DHCP spoofing:** a rogue server hands out false default gateways, false DNS servers, or invalid IP addresses. Mitigated by **DHCP snooping**.

### DHCP Snooping Operation
- **Trusted ports:** interfaces connected to legitimate DHCP servers, switches, or routers. They must be configured explicitly.
- **Untrusted ports:** access ports and other devices outside the network's control.
- **DHCP binding table:** built by the switch from the untrusted ports, binding each host's MAC address, assigned IP address, lease time, VLAN, and interface.

### DHCP Snooping Configuration
```
S1(config)# ip dhcp snooping
S1(config)# interface f0/1
S1(config-if)# ip dhcp snooping trust
S1(config-if)# exit
S1(config)# interface range f0/5 - 24
S1(config-if-range)# ip dhcp snooping limit rate 6
S1(config-if-range)# exit
S1(config)# ip dhcp snooping vlan 5,10,50-52
```

### Verification Commands

| Command | Shows |
|---------|-------|
| `show ip dhcp snooping` | Global settings, enabled VLANs, and interface trust and rate limits |
| `show ip dhcp snooping binding` | The dynamically built IP-to-MAC binding database |

## 4. Mitigate ARP Attacks

### Dynamic ARP Inspection (DAI)
- **ARP spoofing / poisoning:** threat actors send unsolicited ARP replies that tie their MAC address to a target IP (such as the default gateway).
- **DAI requires DHCP snooping**, because it uses the snooping binding database.
- **DAI functions:**
  - Intercepts all ARP requests and replies on untrusted ports.
  - Checks them against valid IP-to-MAC bindings.
  - Drops and logs invalid ARP replies.
  - Error-disables the interface if the configured rate limit is exceeded.

### DAI Configuration
Set access ports as **untrusted** and uplinks to other network devices as **trusted**.

```
S1(config)# ip dhcp snooping
S1(config)# ip dhcp snooping vlan 10
S1(config)# ip arp inspection vlan 10
S1(config)# interface fa0/24
S1(config-if)# ip dhcp snooping trust
S1(config-if)# ip arp inspection trust
```

### Additional DAI Validation Options
- Check extra fields with `ip arp inspection validate {[src-mac] [dst-mac] [ip]}`.
- **Note:** entering several separate validate commands overwrites the earlier ones, so put them all on one line:

```
S1(config)# ip arp inspection validate src-mac dst-mac ip
```

## 5. Mitigate STP Attacks

### PortFast and BPDU Guard
- **STP manipulation:** threat actors spoof root bridge priorities to change the network topology.
- **PortFast:** skips the STP listening and learning states and takes access ports straight to forwarding. Use it **only** on access ports connected to end devices.
- **BPDU Guard:** error-disables an access port immediately if it receives any BPDU frame. Prevents rogue switches or unauthorized access points from being attached.

### PortFast and BPDU Guard Configuration
```
S1(config)# interface fa0/1
S1(config-if)# switchport mode access
S1(config-if)# spanning-tree portfast
S1(config-if)# spanning-tree bpduguard enable
S1(config-if)# exit

! Enable globally on all access ports:
S1(config)# spanning-tree portfast default
S1(config)# spanning-tree portfast bpduguard default
```

### Verification Commands

| Command | Shows |
|---------|-------|
| `show running-config \| begin span` | The STP configuration |
| `show spanning-tree summary` | The global status of the PortFast and BPDU Guard defaults |
| `show spanning-tree interface [type/number] detail` | PortFast status on one interface |

## Exam Reminders
- Port security needs `switchport mode access` first, then `switchport port-security`.
- Violation modes: **shutdown** (default, err-disabled), **restrict** (drops and logs), **protect** (drops silently).
- Sticky MAC addresses go into running-config; save to keep them.
- VLAN hopping fixes: access mode on user ports, `switchport nonegotiate` on trunks, change the native VLAN, move unused ports to an unused VLAN.
- DHCP snooping: trust the uplinks and servers; rate limit access ports. Starvation = port security; spoofing = DHCP snooping.
- DAI needs DHCP snooping; trust the uplink ports.
- PortFast on access ports only; BPDU Guard err-disables a port that receives a BPDU.
