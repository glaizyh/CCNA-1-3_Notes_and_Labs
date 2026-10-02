# Module 3: Protocols and Models

## 1. The Rules

### Communication Fundamentals
Every communication has 3 elements:
- **Source (sender)**
- **Destination (receiver)**
- **Channel (media)** that provides the path

Rules (protocols) must account for:
- An identified sender and receiver
- Common language and grammar
- Speed and timing of delivery
- Confirmation or acknowledgment requirements

### Network Protocol Requirements
- **Encoding:** converting information into another acceptable form for transmission. Decoding reverses the process to interpret the information.
- **Formatting and encapsulation:** a message must use a specific format or structure depending on the message type and the delivery channel.
- **Message size:** messages are converted to bits, which are encoded into a pattern of light, sound, or electrical impulses. The destination host decodes the signals to interpret the message.
- **Message timing:**
  - **Flow control:** manages the rate of data transmission, defining how much information can be sent and how fast.
  - **Response timeout:** manages how long a device waits when it does not hear a reply from the destination.
  - **Access method:** determines when someone can send a message and handles collisions (more than one device sending at the same time, corrupting the messages).
    - Proactive protocols try to prevent collisions.
    - Reactive protocols set up a recovery method after a collision.

### Message Delivery Options

| Type | Description |
|------|-------------|
| Unicast | One-to-one |
| Multicast | One-to-many, typically not all |
| Broadcast | One-to-all (used in IPv4, **not** an option in IPv6) |

## 2. Protocols

Network protocols define a common set of rules. They can be implemented in software, hardware, or both, and each has its own function, format, and rules.

### Protocol Types

| Type | Description |
|------|-------------|
| Network communications | Let two or more devices communicate over one or more networks |
| Network security | Secure data with authentication, data integrity, and data encryption |
| Routing | Let routers exchange route information, compare paths, and select the best path |
| Service discovery | Automatic detection of devices or services |

### Protocol Functions
- **Addressing:** identifies sender and receiver.
- **Reliability:** provides guaranteed delivery.
- **Flow control:** keeps data flowing at an efficient rate.
- **Sequencing:** uniquely labels each transmitted segment of data.
- **Error detection:** determines whether data became corrupted during transmission.
- **Application interface:** process-to-process communication between network applications.

### Protocol Interaction Example

| Protocol | Role |
|----------|------|
| HTTP (Hypertext Transfer Protocol) | Governs how a web server and web client interact; defines content and format |
| TCP (Transmission Control Protocol) | Manages individual conversations, provides guaranteed delivery, manages flow control |
| IP (Internet Protocol) | Delivers messages globally from sender to receiver |
| Ethernet | Delivers messages from one NIC to another NIC on the same Ethernet LAN |

## 3. Protocol Suites

- **Protocol suite:** a group of inter-related protocols needed to perform a communication function; sets of rules that work together to solve a problem.
- **TCP/IP (Internet Protocol Suite):** the most common protocol suite, maintained by the IETF.
- **Other suites:**
  - **OSI protocols:** developed by ISO and ITU.
  - **AppleTalk:** proprietary suite from Apple Inc.
  - **Novell NetWare:** proprietary suite from Novell Inc.

### TCP/IP Protocol Suite
TCP/IP is an **open standard** protocol suite, freely available to the public and endorsed by the networking industry to ensure interoperability.

| TCP/IP layer | Protocols |
|--------------|-----------|
| Application | DNS, DHCPv4, DHCPv6, SLAAC, SMTP, POP3, IMAP, FTP, SFTP, TFTP, REST, HTTP, HTTPS |
| Transport | TCP (connection-oriented), UDP (connectionless) |
| Internet | IPv4, IPv6, NAT, ICMPv4, ICMPv6, ICMPv6 ND, OSPF, EIGRP, BGP |
| Network access | ARP, Ethernet, WLAN |

## 4. Standards Organizations

Vendor-neutral, non-profit organizations develop and promote open standards to encourage interoperability, competition, and innovation.

| Organization | Role |
|--------------|------|
| **ISOC** (Internet Society) | Promotes the open development and evolution of the internet |
| **IAB** (Internet Architecture Board) | Manages and develops internet standards |
| **IETF** (Internet Engineering Task Force) | Develops, updates, and maintains internet and TCP/IP technologies |
| **IRTF** (Internet Research Task Force) | Long-term research on internet and TCP/IP protocols |
| **ICANN** (Internet Corporation for Assigned Names and Numbers) | Coordinates IP address allocation, domain name management, and other assigned information |
| **IANA** (Internet Assigned Numbers Authority) | Oversees IP address allocation, domain name management, and protocol identifiers for ICANN |
| **IEEE** (Institute of Electrical and Electronics Engineers) | Standards in power and energy, healthcare, telecommunications, and networking |
| **EIA** (Electronic Industries Alliance) | Standards for electrical wiring, connectors, and 19-inch racks |
| **TIA** (Telecommunications Industry Association) | Standards for radio equipment, cellular towers, VoIP devices, satellite communications, and more |
| **ITU-T** (International Telecommunications Union, Telecommunication Standardization Sector) | Standards for video compression, IPTV, and broadband communications (such as DSL) |

## 5. Reference Models

### Benefits of a Layered Model
- Helps protocol design, since protocols at a layer have defined information to act on and a defined interface to the layers above and below.
- Fosters competition because products from different vendors can work together.
- Prevents changes in one layer from affecting the layers above and below.
- Provides a common language to describe networking functions and capabilities.

### OSI Model vs. TCP/IP Model

| # | OSI layer | OSI description | TCP/IP layer |
|---|-----------|-----------------|--------------|
| 7 | Application | Protocols for process-to-process communications | Application |
| 6 | Presentation | Common representation of data transferred between application services | Application |
| 5 | Session | Services to manage data exchange | Application |
| 4 | Transport | Services to segment, transfer, and reassemble data | Transport |
| 3 | Network | Services to exchange individual pieces of data over the network | Internet |
| 2 | Data Link | Methods for exchanging data frames over a common medium | Network Access |
| 1 | Physical | Means to activate, maintain, and de-activate physical connections | Network Access |

TCP/IP layer descriptions:
- **Application:** represents data to the user, plus encoding and dialog control.
- **Transport:** supports communication between various devices across diverse networks.
- **Internet:** determines the best path through the network.
- **Network access:** controls the hardware devices and media that make up the network.

## 6. Data Encapsulation

### Segmenting and Sequencing
- **Segmenting:** breaking messages into smaller units.
  - Increases **speed**: large amounts of data can be sent without tying up a link.
  - Increases **efficiency**: only segments that fail to arrive need to be retransmitted.
- **Multiplexing:** taking multiple streams of segmented data and interleaving them.
- **Sequencing:** numbering segments so the message can be reassembled at the destination (TCP is responsible for sequencing).

### Encapsulation and PDUs
- **Encapsulation:** top-down process where each protocol adds its information as data moves down the stack.
- **De-encapsulation:** bottom-up process where each layer strips its header and passes the data up until the application can process it.

| Layer | PDU |
|-------|-----|
| Application | **Data** (data stream) |
| Transport | **Segment** |
| Network | **Packet** |
| Data Link | **Frame** (medium dependent) |
| Physical | **Bits** (bit stream) |

## 7. Data Access

### Addressing Roles
- **Network layer addresses (Layer 3):** deliver the IP packet from the original source to the final destination.
  - **Source IP address:** the sending device (original source).
  - **Destination IP address:** the receiving device (final destination).
  - Each IP address has a **network portion/prefix** (network group) and a **host portion/interface ID** (specific device in that group).
- **Data link layer addresses (Layer 2):** MAC addresses, physically embedded in the Ethernet NIC. They deliver frames from one NIC to another NIC on the **same network**.

### Devices on the Same Network
- Source and destination have the same number in the network portion of the address.
- The frame uses the **actual MAC address of the destination NIC**.

### Devices on a Remote Network
- Source and destination have different network portions.
- **Default gateway (DGW):** the router interface IP address on the local LAN; the "door" to all other remote locations.

### Address Behavior Across Hops
- **Layer 3 IP addresses** stay the same from original source to final destination (they are global).
- **Layer 2 MAC addresses** change at every hop.

| Hop | Source MAC | Destination MAC |
|-----|------------|-----------------|
| First hop | PC1 NIC | Local default gateway interface |
| Second hop | First router exit interface | Second router interface |
| Last hop | Second router exit interface | Web server NIC |
