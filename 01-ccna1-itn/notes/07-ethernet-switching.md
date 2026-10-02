# Module 7: Ethernet Switching

## 1. Ethernet Frames

### Ethernet Encapsulation
- Ethernet operates at the **data link layer** and the **physical layer**.
- It is a family of networking technologies defined in the IEEE 802.2 and 802.3 standards.

### Data Link Sublayers
The 802 LAN/MAN standards, including Ethernet, use two sublayers of the data link layer:

| Sublayer | Standard | Role |
|----------|----------|------|
| **LLC** | IEEE 802.2 | Places information in the frame to identify which network layer protocol is used |
| **MAC** | IEEE 802.3, 802.11, or 802.15 | Handles data encapsulation and media access control, and provides data link layer addressing |

### MAC Sublayer Functions

**Data encapsulation**
1. **Ethernet frame:** the internal structure of the frame.
2. **Ethernet addressing:** a source and destination MAC address deliver the frame from NIC to NIC on the same LAN.
3. **Ethernet error detection:** the Frame Check Sequence (FCS) trailer is used to detect errors.

**Media access:** specifications for the different Ethernet standards over copper and fiber.
1. **Legacy Ethernet:** bus topology or hubs, on a shared, half-duplex medium using CSMA/CD.
2. **Modern Ethernet LANs:** switches operating in full-duplex, which don't need CSMA/CD access control.

### Ethernet Frame Fields
Minimum frame size is **64 bytes** and maximum is **1518 bytes** (excluding the preamble).

| Field | Size | Description |
|-------|------|-------------|
| Preamble and SFD | 8 bytes | Synchronization between sending and receiving devices (not counted in the frame size) |
| Destination MAC address | 6 bytes | Physical address of the receiving node |
| Source MAC address | 6 bytes | Physical address of the transmitting node |
| Type / Length | 2 bytes | Identifies the upper-layer protocol encapsulated in the frame |
| Data | 46-1500 bytes | Frame payload (the encapsulated packet) |
| FCS | 4 bytes | Frame Check Sequence, used to detect errors |

- **Runt frame / collision fragment:** any frame under 64 bytes; automatically discarded by receiving devices.
- **Jumbo / baby giant frames:** frames with more than 1500 bytes of data.
- A frame smaller than the minimum or larger than the maximum is dropped by the receiver as invalid.

## 2. Ethernet MAC Address

### MAC Address Characteristics
- A MAC address is a **48-bit** binary value, written as **12 hexadecimal digits** (6 bytes).
- Every MAC address must be unique to the Ethernet device or interface.
- **OUI (Organizationally Unique Identifier):** a 6-hex-digit (24-bit, 3-byte) vendor code assigned by the IEEE.
- A MAC address = 6 hex digits of **vendor OUI** + 6 hex digits of **vendor-assigned value**.
- Hex values are often written with `0x` in front (e.g., `0x73`), a subscript 16, or an `H` after (e.g., `73H`) to tell them apart from decimal.

### Frame Processing
1. When a NIC receives a frame, it compares the destination MAC address with its own MAC address stored in RAM.
2. If there is **no match**, the device discards the frame.
3. If there is a **match**, it passes the frame up the OSI layers for de-encapsulation.
4. NICs also accept frames sent to a broadcast address, or to a multicast group the host belongs to.

### Layer 2 Communication Types

| Type | Description |
|------|-------------|
| **Unicast** | One sender to one receiver. The **source** MAC address must always be unicast |
| **Broadcast** | Received and processed by every device on the LAN |
| **Multicast** | Received and processed by a group of devices in the same multicast group |

**Unicast**
- IPv4 uses **ARP** to discover destination MAC addresses.
- IPv6 uses **ND (Neighbor Discovery)** to discover destination MAC addresses.

**Broadcast**
- Destination MAC: `FF-FF-FF-FF-FF-FF` (48 ones in binary).
- Flooded out all switch ports except the incoming port.
- Not forwarded by routers.

**Multicast**
- IPv4 multicast MAC addresses begin with `01-00-5E`.
- IPv6 multicast MAC addresses begin with `33-33`.
- Flooded out all ports except the incoming port, unless multicast snooping is configured.

## 3. The MAC Address Table

### Switch Fundamentals
- A Layer 2 Ethernet switch uses **Layer 2 MAC addresses** to make forwarding decisions.
- It is unaware of the upper-layer payload protocols (IPv4, ARP, IPv6 ND).
- When a switch is turned on, its **MAC address table** (also called the **CAM**, Content Addressable Memory, table) is **empty**.

### Learning and Forwarding Process

**1. Examine the source MAC address (learn)**
- Check every incoming frame's source MAC address and port number.
- Source MAC **not in the table:** add it with the port number.
- Source MAC **already in the table:** refresh its timer (default timeout is 5 minutes).
- Source MAC in the table on a **different port:** update it with the new port number.

**2. Find the destination MAC address (forward)**
- Destination MAC **in the table:** forward the frame out that port (filtering).
- Destination MAC **not in the table:** flood the frame out all ports except the incoming one (unknown unicast).
- **Broadcast and multicast** frames are also flooded out all ports except the incoming one.

## 4. Switch Speeds and Forwarding Methods

### Frame Forwarding Methods

**Store-and-forward**
- Receives the entire frame and computes the CRC error check.
- If valid, looks up the destination address and forwards the frame out the correct port.
- Discards corrupt frames, so invalid data doesn't use up bandwidth.
- Required for QoS analysis (e.g., prioritizing VoIP over web traffic).

**Cut-through**
- Forwards the frame as soon as the destination MAC address is read, before the whole frame arrives.
- Does **not** do error checking.

| Cut-through variant | Description |
|---------------------|-------------|
| **Fast-forward** | Lowest latency. Forwards immediately after reading the destination MAC address. May relay faulty frames |
| **Fragment-free** | Reads and checks the first 64 bytes (where most collisions occur) before forwarding |

### Memory Buffering Methods
- **Port-based memory:** frames are stored in queues linked to specific incoming and outgoing ports. One frame can delay all the others in the queue if its destination port is busy.
- **Shared memory:** all frames go into a common memory buffer shared by all ports, allocated dynamically as needed. Enables asymmetric switching (ports with different data rates).

### Port Settings and Duplex Modes
1. **Full-duplex:** both ends send and receive at the same time. Gigabit Ethernet ports operate only in full-duplex.
2. **Half-duplex:** only one end can send at a time.
3. **Duplex mismatch:** one port is half-duplex and the other full-duplex, causing performance problems and collisions on 10/100 Mbps links.
4. **Autonegotiation:** connected devices automatically choose the best speed and duplex.
5. **Auto-MDIX:** automatically detects the required cable type (straight-through or crossover) and configures the interface. Re-enable it with:

```
Switch(config-if)# mdix auto
```

