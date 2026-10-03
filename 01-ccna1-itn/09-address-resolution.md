# Module 9: Address Resolution

## 1. MAC and IP

### Destination on the Same Network
- **Layer 2 physical address (MAC):** used for NIC-to-NIC communication on the same Ethernet network.
- **Layer 3 logical address (IP):** used to send the packet from the source device to the destination device.
- Layer 2 addresses deliver frames from one NIC to another NIC on the **same network**.
- If the destination IP address is on the same network, the destination MAC address is the **destination device's own MAC**.

### Destination on a Remote Network
- If the destination IP address is on a remote network, the destination MAC address is that of the **default gateway** (the router interface).
- **IPv4** uses **ARP** to match a device's IPv4 address with the MAC address of its NIC.
- **IPv6** uses **ICMPv6** (Neighbor Discovery) to match a device's IPv6 address with the MAC address of its NIC.

| Destination | Destination IP | Destination MAC |
|-------------|----------------|-----------------|
| Same network | The destination device | The destination device |
| Remote network | The destination device | The default gateway (router interface) |

## 2. ARP

### Purpose and Functions
A device uses ARP to find the destination MAC address of a **local** device when it knows the device's IPv4 address.

Two basic functions:
1. Resolving IPv4 addresses to MAC addresses.
2. Maintaining an ARP table of IPv4-to-MAC mappings.

### ARP Operation
1. To send a frame, the device looks in its **ARP table** for the destination IPv4 address and its MAC address.
   - Destination on the **same network:** it looks for the destination's IPv4 address.
   - Destination on a **different network:** it looks for the **default gateway's** IPv4 address.
2. If the entry is found, that MAC address is used as the destination MAC in the frame.
3. If **no entry** is found, the device sends an **ARP request**.
4. After the **ARP reply** arrives, the device adds the IPv4 address and MAC address to its ARP table.

### Removing Entries from an ARP Table
- Entries aren't permanent. They are removed when the **ARP cache timer** expires after a set time without use.
- The timer length differs depending on the operating system.
- An administrator can also remove entries manually.

### Viewing ARP Tables

| Device | Command |
|--------|---------|
| Cisco router | `show ip arp` |
| Windows PC | `arp -a` |

### ARP Issues
- **Broadcast overhead:** every device on the local network receives and processes ARP requests. Too many ARP broadcasts can reduce performance.
- **ARP spoofing / poisoning:** a threat actor can spoof ARP replies to carry out an ARP poisoning attack.
- **Mitigation:** enterprise-level switches include techniques to protect against ARP attacks.

## 3. IPv6 Neighbor Discovery

### Neighbor Discovery (ND) Messages
- IPv6 does **not** use ARP. It uses the **Neighbor Discovery (ND)** protocol to resolve MAC addresses.
- ND provides three services:
  - Address resolution
  - Router discovery
  - Redirection

| Message | Used for |
|---------|----------|
| **Neighbor Solicitation (NS)** and **Neighbor Advertisement (NA)** | Device-to-device messaging, such as address resolution |
| **Router Solicitation (RS)** and **Router Advertisement (RA)** | Messaging between devices and routers for router discovery |
| **Redirect** | Used by routers to select a better next hop |

All of these are ICMPv6 messages.

### Address Resolution with ND
- IPv6 devices use ND to find the MAC address of a known IPv6 address.
- **NS messages** are sent using special Ethernet and IPv6 **multicast** addresses.
- The target node replies with an **NA message** containing its MAC address.

## Exam Reminders
- Same network: destination MAC = the destination device. Remote network: destination MAC = the default gateway.
- IPv4 uses **ARP**; IPv6 uses **ND** (ICMPv6 NS/NA).
- ARP request = broadcast; ARP reply = unicast to the requester.
- ARP table commands: `arp -a` (Windows), `show ip arp` (Cisco router).
- ARP entries time out; ARP spoofing is a known attack.
- RS/RA = router discovery; NS/NA = address resolution.
