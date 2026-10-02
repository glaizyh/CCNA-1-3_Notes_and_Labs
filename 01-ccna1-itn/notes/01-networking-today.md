# Module 1: Networking Today

## 1. Networks Affect Our Lives
- Communication is almost as vital as air, water, food, and shelter.
- Networks create a world without boundaries, fostering global communities and a "human network".

## 2. Network Components

### Host Roles and Architectures
- **Hosts / End devices:** any computer connected to the network that directly takes part in network communication.
- **Client-server model**
  - **Servers:** run specialized software to provide services/information to end devices (email, web, file servers).
  - **Clients:** run software that sends requests to servers to retrieve information.
- **Peer-to-peer (P2P)**
  - A device can act as both client and server at the same time.
  - Suited to very small networks doing simple tasks like file or printer sharing.
  - Pros: easy to set up, less complex, lower cost.
  - Cons: no centralized administration, less secure, not scalable, slower performance.

### Network Devices and Media
- **End devices:** where messages originate or terminate (desktops, laptops, IP phones, printers, tablets).
- **Intermediary devices:** connect end devices and manage the flow of data (switches, wireless access points, routers, firewalls). Functions:
  - Regenerate and retransmit data signals.
  - Maintain information about network pathways.
  - Notify other devices of errors and communication failures.
- **Network media:**

| Media | Signal used |
|-------|-------------|
| Copper cable | Electrical impulses |
| Fiber-optic cable | Pulses of light |
| Wireless | Electromagnetic wave frequency modulation |

### Network Representations and Topologies
- **NIC (Network Interface Card):** physically connects the end device to the network.
- **Physical port and interface:** used interchangeably to refer to a connector on a network device.

### Topology Diagrams
- **Physical topology:** shows the physical location of intermediary devices and cable installation.
- **Logical topology:** shows device IP addresses, ports, and the overall logical addressing scheme.

## 3. Common Types of Networks

### Sizes of Networks
- **Small home:** connects a few local computers to each other and the internet.
- **SOHO (Small Office/Home Office):** lets computers in a home or remote office connect to a corporate network.
- **Medium to large:** spans many locations with hundreds or thousands of interconnected computers.
- **Worldwide:** connects hundreds of millions of computers globally (the internet).

### LAN vs. WAN

| Feature | LAN | WAN |
|---------|-----|-----|
| Coverage area | Small/limited geographical area | Wide geographical area |
| Administration | Single organization or individual | One or more service providers (SPs) |
| Speed / bandwidth | High-speed bandwidth to internal devices | Typically slower-speed links between LANs |

### Internet, Intranet, and Extranet
- **Internet:** global collection of interconnected LANs and WANs, maintained by groups like IETF, ICANN, and IAB.
- **Intranet:** private collection of LANs/WANs internal to an organization, accessible only to authorized members.
- **Extranet:** gives secure access to an organization's network for trusted external individuals or partner organizations.

## 4. Internet Connections

### SOHO (Home/Small Office)
- **Cable:** high bandwidth, always-on, offered by cable TV providers.
- **DSL (Digital Subscriber Line):** high bandwidth, always-on, runs over traditional telephone lines.
- **Cellular:** connects through a mobile cell phone network.
- **Satellite:** useful in rural areas without terrestrial ISPs.
- **Dial-up telephone:** inexpensive, low bandwidth, uses standard telephone lines and a modem.

### Business Connections
- **Dedicated leased line:** reserved private circuits within a provider network for voice/data.
- **Ethernet WAN / Metro Ethernet:** extends LAN access technology into WAN links.
- **Business DSL:** symmetric formats (like SDSL) with equal upload/download speeds.
- **Satellite:** used when wired solutions are unavailable.
- **Fiber internet:** broadband over fiber-optic cable with high speed and symmetrical upload/download.

### Converged Networks
- **Legacy networks:** separately cabled systems for phone, video, and data, each with its own rules and standards.
- **Converged networks:** deliver data, voice, and video over one network infrastructure using the same rules and standards.

## 5. Reliable Networks (Network Architecture)
Network architecture refers to the technologies, standards, and rules that move data. It must meet 4 core requirements:

1. **Fault tolerance**
   - Limits the impact of failure using redundant, alternative paths.
   - Uses **packet switching** (traffic split into packets routed independently) rather than circuit switching.
2. **Scalability**
   - Expands quickly to support new users and applications without degrading existing performance.
3. **Quality of Service (QoS)**
   - Manages data and voice traffic flows.
   - Prioritizes time-sensitive traffic (e.g., VoIP calls) over less sensitive traffic (e.g., web).
4. **Security**
   - **Network infrastructure and physical security:** protecting hardware and preventing unauthorized access.
   - **Information security:** protecting data transmitted over the network.
   - **CIA triad:**
     - **Confidentiality:** only intended recipients can read the data.
     - **Integrity:** data has not been altered in transit.
     - **Availability:** timely and reliable data access for authorized users.

## 6. Network Trends
- **BYOD (Bring Your Own Device):** users use personal laptops, tablets, and smartphones to access network resources anywhere.
- **Online collaboration:** tools like Cisco Webex and Webex Teams for messaging, file sharing, and joint work.
- **Video communication:** real-time video conferencing (e.g., Cisco TelePresence).
- **Cloud computing:** store files and run applications on internet-based server infrastructure in data centers.
  - **Public cloud:** open to the general public (free or pay-per-use).
  - **Private cloud:** dedicated to a specific organization or government entity.
  - **Hybrid cloud:** a combination of two or more cloud types.
  - **Custom cloud:** built for specific industry needs (e.g., healthcare).
- **Smart home technology:** integrates everyday appliances with network capabilities.
- **Powerline networking:** sends network data over existing home electrical wiring when cabling or Wi-Fi is impractical.
- **Wireless broadband (WISP):** connects subscribers using antennas to wireless hotspots or cellular towers.

## 7. Network Security

### Threat Categories
- **External threats:** originate from outside the network perimeter.
- **Internal threats:** originate from inside the organization's network.

### Threat Types
| Threat | Description |
|--------|-------------|
| Viruses, worms, Trojan horses | Malicious software that infects devices, spreads across systems, or disguises itself as a legitimate program |
| Spyware and adware | Software installed on a host that secretly monitors activity or shows unwanted ads |
| Zero-day attacks | Attacks that exploit an unknown vulnerability before a fix exists |
| Threat actor attacks | Attacks carried out by malicious individuals targeting devices or network resources |
| Denial of Service (DoS) | Slows down or crashes network services and devices for legitimate users |
| Data interception and theft | Capturing or stealing private data as it travels across the network |
| Identity theft | Stealing credentials to impersonate an authorized person |
| Lost or stolen devices | Devices holding sensitive data or credentials fall into unauthorized hands |
| Accidental misuse by employees | Unintentional disruption or data exposure by non-malicious staff |
| Malicious employees | Insiders who intentionally abuse access to damage systems or steal data |

### Security Solutions
- **Home / small office:** antivirus/antispyware on hosts, basic router firewall filtering.
- **Larger / enterprise networks:**
  - Dedicated firewall systems
  - Access Control Lists (ACLs)
  - Intrusion Prevention Systems (IPS)
  - Virtual Private Networks (VPNs)
