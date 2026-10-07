# Module 7: WAN Concepts

## 1. Purpose of WANs

### LANs vs. WANs

| | LAN (Local Area Network) | WAN (Wide Area Network) |
|--|--------------------------|-------------------------|
| **Coverage** | A small geographic area, connecting local computers and peripherals | Large geographic areas, connecting remote users, networks, and sites beyond the LAN boundary |
| **Owner** | An organization or home user; no usage fees | ISPs, telephone, cable, and satellite providers; a fee is charged |
| **Bandwidth** | High, over Ethernet or Wi-Fi | Varies, over long distances |

### Private vs. Public WANs
- **Private WAN:** a dedicated connection for a single customer, with guaranteed service level agreements (SLAs), consistent bandwidth, and high security.
- **Public WAN:** provided by service providers or ISPs over the public internet infrastructure. Service levels, bandwidth, and security vary, and aren't guaranteed without encryption.

### Logical WAN Topologies

| Topology | Description |
|----------|-------------|
| **Point-to-point** | A direct circuit between two endpoints, using a Layer 2 transport service. Transparent to the customer networks; can get expensive if many connections are needed |
| **Hub-and-spoke** | One hub router interface is shared by several spoke circuits through virtual circuits and subinterfaces. Spokes can only talk through the hub, which is a single point of failure |
| **Dual-homed** | Adds redundancy and load balancing by connecting spoke or hub sites with extra hardware and multiple connections. More expensive and more complex to configure |
| **Fully meshed** | Uses multiple virtual circuits to connect every site directly to every other site. Highest fault tolerance |
| **Partially meshed** | Connects many, but not all, sites directly, balancing cost and redundancy |

### Carrier Connection Topologies

| Topology | Description |
|----------|-------------|
| **Single-homed** | One link to one ISP. Inexpensive, but no redundancy |
| **Dual-homed** | Two links to a single ISP, for redundancy and load balancing |
| **Multihomed** | A single link to each of two different ISPs, for ISP-level redundancy and load balancing |
| **Dual-multihomed** | Redundant links to multiple different ISPs. Maximum redundancy and resilience, at the highest cost |

## 2. WAN Operations and Standards

### Standards and OSI Model Layers
- **Standards organizations:** TIA/EIA, ISO, and IEEE.
- **Layer 1 (physical) protocols:** Synchronous Digital Hierarchy (SDH), Synchronous Optical Networking (SONET), and Dense Wavelength Division Multiplexing (DWDM).
- **Layer 2 (data link) protocols:** broadband (DSL, cable), wireless, Ethernet WAN (Metro Ethernet), MPLS, PPP, HDLC, and legacy protocols (Frame Relay, ATM).

### Common WAN Terminology

| Term | Description |
|------|-------------|
| **DTE** (Data Terminal Equipment) | The customer device (e.g., a router) that connects the subscriber LAN to the WAN communication device |
| **DCE** (Data Communications Equipment) | A device (e.g., a modem or CSU/DSU) that connects the DTE to the service provider network |
| **CPE** (Customer Premises Equipment) | The DTE and DCE devices located on the subscriber's premises |
| **POP** (Point-of-Presence) | The physical connection point where the subscriber connects to the provider network |
| **Demarcation point** | The physical location that separates the customer's CPE from the service provider's equipment |
| **Local loop** (last mile) | The copper or fiber wiring that connects the CPE to the provider's Central Office (CO) |
| **Central Office (CO)** | The local service provider facility that connects subscriber local loops to the provider network |
| **Toll / backbone network** | Long-haul, high-capacity fiber lines, switches, and core routers inside the provider network |

### Communication Methods
- **Serial transmission:** bits are sent one after another over a single channel; ideal for long-distance WAN links.
- **Parallel transmission:** several bits are sent at once over several wires; limited to very short distances because of signal timing problems over longer lengths.
- **Circuit-switched:** sets up a dedicated physical or virtual channel between endpoints before communication starts (e.g., PSTN, ISDN).
- **Packet-switched:** splits traffic into packets that are routed dynamically over a shared network (e.g., Metro Ethernet, MPLS).

## 3. Traditional WAN Connectivity

### Leased Lines
- Dedicated point-to-point serial connections rented from a provider for a monthly fee.

| Carrier | Standard | Speed |
|---------|----------|-------|
| **T-carrier** (North America) | T1 | up to **1.544 Mbps** |
| | T3 | up to **43.7 Mbps** (the course figure; the standard T3 rate is 44.736 Mbps) |
| **E-carrier** (Europe) | E1 | up to **2.048 Mbps** |
| | E3 | up to **34.368 Mbps** |

- **Pros:** simple to maintain, high quality, and always available.
- **Cons:** expensive, inflexible, and fixed capacity.

### Legacy Switched Technologies
- **PSTN / dial-up:** voiceband modems, limited to speeds under **56 kbps** over copper telephone lines.
- **ISDN:** circuit switching over copper local loops, with data rates from **45 kbps** to **2.048 Mbps**.
- **Frame Relay:** a Layer 2 non-broadcast multi-access (NBMA), packet-switched technology that uses Permanent Virtual Circuits (PVCs) identified by Data Link Connection Identifiers (DLCIs).
- **ATM (Asynchronous Transfer Mode):** a cell-based architecture that uses fixed-length **53-byte cells** for voice, video, and data.

## 4. Modern WAN Connectivity Options

### Dedicated Broadband and Packet-Switched
- **Dark fiber:** optical fiber infrastructure leased or bought by a company to connect remote sites directly.
- **Ethernet WAN (Metro Ethernet / Metro E):** a high-speed optical Layer 2 network service that replaces traditional serial links. It lowers cost, makes LAN-to-WAN integration simple, and scales better.
- **MPLS (Multiprotocol Label Switching):** a high-performance service provider routing technology that forwards packets using short labels. Supports many access methods (DSL, cable, Ethernet) and carries IPv4 and IPv6 traffic.
  - **MPLS router roles:** Customer Edge (CE), Provider Edge (PE), and Provider Core (P).

### Internet-Based Wired Broadband

| Technology | Description |
|------------|-------------|
| **DSL** (Digital Subscriber Line) | Always-on technology over existing twisted-pair phone lines |
| **ADSL** | More downstream than upstream bandwidth (asymmetric) |
| **SDSL** | Equal upstream and downstream bandwidth (symmetric) |
| **DSLAM** | At the provider's CO; combines many customers' DSL connections. A dedicated, non-shared medium |
| **PPPoE** (Point-to-Point Protocol over Ethernet) | Used by ISPs for subscriber authentication, dynamic IP allocation, and link management |
| **Cable** | Uses coaxial cable and the **DOCSIS** standard. Cable modems talk to a Cable Modem Termination System (CMTS) at the provider headend, through optical nodes. Bandwidth is **shared** among local subscribers |
| **FTTx** (Fiber-to-the-x) | The highest bandwidth, over optical fiber. Includes FTTH (home), FTTB (building), and FTTN (node/neighborhood) |

### Internet-Based Wireless Broadband
- **Cellular broadband:** connects remote sites or users through mobile towers, using 3G/4G/5G and LTE.
- **Satellite internet:** directional dishes aimed at geostationary satellites. Good for remote or rural areas with no terrestrial access, but weather can disrupt the line of sight.
- **Municipal Wi-Fi:** wireless mesh networks provided across cities or public spaces.
- **WiMAX (IEEE 802.16):** high-speed wireless broadband with wide coverage, similar to cellular networks.

### VPN Technology over the Public Internet
- A VPN encrypts traffic sent across public networks, giving cost-effective, secure connectivity.
- **Site-to-site VPN:** configured on perimeter routers or firewalls; encrypts site-to-site traffic transparently to end users.
- **Remote access VPN:** client software or a web browser (HTTPS) lets mobile users and teleworkers set up an encrypted tunnel back to the corporate network.

## Exam Reminders
- LAN = owned by you, fast, short range. WAN = rented from a provider, long range.
- Logical topologies: point-to-point, hub-and-spoke, dual-homed, full mesh (highest fault tolerance), partial mesh.
- Carrier connections: single-homed, dual-homed, multihomed, dual-multihomed.
- DTE = customer side (router); DCE = provider side (modem, CSU/DSU). Demarcation point separates CPE from the provider.
- Leased lines: T1 = 1.544 Mbps, E1 = 2.048 Mbps. ATM = 53-byte cells.
- Circuit-switched (PSTN, ISDN) vs. packet-switched (Metro Ethernet, MPLS).
- DSL: ADSL (asymmetric), SDSL (symmetric). Cable uses DOCSIS and shares bandwidth.
- MPLS roles: CE, PE, P.
- VPNs: site-to-site (between routers/firewalls) vs. remote access (client or browser).
