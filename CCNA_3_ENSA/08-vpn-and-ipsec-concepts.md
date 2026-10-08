# Module 8: VPN and IPsec Concepts

## 1. VPN Technology

### VPN Overview and Definition
- **Virtual Private Network (VPN):** creates an end-to-end private network connection over a public network (such as the internet).
- **"Virtual":** the information travels inside a private network structure while actually being carried over public infrastructure.
- **"Private":** the traffic is encrypted to keep the data confidential while it crosses the public network.

### Benefits of VPN Technology

| Benefit | Description |
|---------|-------------|
| **Cost savings** | Cuts connectivity costs and raises remote connection bandwidth by using public internet access |
| **Security** | Uses advanced encryption and authentication protocols (IPsec, SSL) to protect data from unauthorized access |
| **Scalability** | New users and sites can be added without big infrastructure investments |
| **Compatibility** | Works across a wide variety of WAN link options, including broadband |

### Enterprise vs. Service Provider VPNs
- **Enterprise-managed VPNs:** created and managed by the enterprise itself over the internet, using IPsec and SSL.
- **Service provider-managed VPNs:** created and managed inside the provider's network, using Multiprotocol Label Switching (MPLS) at Layer 2 or Layer 3 to keep customer traffic separate.
  - *Legacy solutions:* Frame Relay and Asynchronous Transfer Mode (ATM).

## 2. Types of VPNs

### Remote-Access VPNs
Dynamic connections between an individual remote client and a corporate VPN terminating device.
- **Clientless SSL VPN:** the connection is set up and secured through a standard web browser, using SSL/TLS.
- **Client-based VPN:** needs dedicated VPN client software on the user's device (e.g., Cisco AnyConnect Secure Mobility Client).

### IPsec vs. SSL Remote Access

| Feature | IPsec | SSL |
|---------|-------|-----|
| **Applications supported** | Extensive: all IP-based applications | Limited: web-based applications and file sharing |
| **Authentication** | Strong: two-way, using pre-shared keys or digital certificates | Moderate: one-way or two-way |
| **Encryption strength** | Strong: key lengths of 56 to 256 bits | Moderate to strong: key lengths of 40 to 256 bits |
| **Connection complexity** | Medium: needs a VPN client installed on the host | Low: needs only a standard web browser |
| **Connection options** | Limited: only specific pre-configured devices can connect | Extensive: any device with a browser can connect |

### Site-to-Site IPsec VPNs
- **Operation:** connects whole networks across an untrusted infrastructure.
- **Transparency:** end hosts send and receive unencrypted traffic and don't know about the VPN. The traffic is encapsulated and encrypted only between the local and remote VPN gateways.

### GRE over IPsec
- **Generic Routing Encapsulation (GRE):** a non-secure site-to-site tunneling protocol that can encapsulate network layer protocols, multicast, and broadcast traffic.
- **Limitation and remedy:** standard IPsec VPNs encrypt only unicast IP traffic. Putting GRE inside IPsec lets dynamic routing updates that use multicast (such as OSPF) pass securely through an IPsec tunnel.

| Term | Meaning |
|------|---------|
| **Passenger protocol** | The original packet being encapsulated (e.g., an IPv4/IPv6 packet or an OSPF update) |
| **Carrier protocol** | The GRE protocol that encapsulates the passenger packet |
| **Transport protocol** | The underlying IP protocol that delivers the encapsulated packet |

### Dynamic Multipoint VPN (DMVPN)
- **Purpose:** a Cisco software solution for building dynamic, scalable VPNs across many branch sites.
- **mGRE integration:** uses Multipoint GRE (mGRE), so a single GRE interface can handle multiple IPsec tunnels dynamically in a hub-and-spoke design.
- **Spoke-to-spoke tunnels:** spokes can build direct tunnels to each other dynamically, without going through the hub.

### IPsec Virtual Tunnel Interface (VTI)
- **Operation:** applies the IPsec configuration directly to a virtual interface, instead of mapping static sessions to physical interfaces.
- **Advantage:** carries encrypted IP unicast and multicast traffic directly, with no extra GRE tunnel configuration.

### Service Provider MPLS VPNs

| Type | Description |
|------|-------------|
| **Layer 3 MPLS VPN** | The provider takes part in the customer's routing, by peering its routers directly with the customer edge routers |
| **Layer 2 MPLS VPN** | The provider uses Virtual Private LAN Service (VPLS) to emulate an Ethernet multiaccess LAN segment. The provider's routers don't take part in customer routing |

## 3. IPsec Framework and Security Services

### Core Security Functions

| Function | How it's provided |
|----------|-------------------|
| **Confidentiality** | Encryption algorithms stop unauthorized viewing of packets |
| **Integrity** | Hashing algorithms make sure packets aren't altered in transit |
| **Origin authentication** | Internet Key Exchange (IKE) verifies the identities of the sender and receiver |
| **Diffie-Hellman (DH)** | Allows secure key exchange over an untrusted medium |

### IPsec Framework Choices
The open framework lets you choose a specific algorithm for each function. Together, the choices make up a **Security Association (SA)**.

**1. IPsec protocol (encapsulation)**
- **Authentication Header (AH):** provides data authentication and integrity, but **no confidentiality** (no encryption).
- **Encapsulating Security Payload (ESP):** provides confidentiality (encryption) as well as authentication and integrity.

**2. Confidentiality (symmetric encryption)**
- **DES:** 56-bit key (legacy, insecure).
- **3DES:** three 56-bit keys (legacy).
- **AES:** the standard algorithm; 128-, 192-, or 256-bit keys.
- **SEAL:** a stream cipher with a 160-bit key.

**3. Integrity (hashing)**
- **Message-Digest 5 (MD5):** uses a 128-bit key.
- **Secure Hash Algorithm (SHA):** uses a 160-bit key or larger.

**4. Authentication**
- **Pre-Shared Key (PSK):** a shared secret key entered manually into each peer.
- **RSA:** uses digital certificates and public key infrastructure (PKI) to authenticate peers.

**5. Diffie-Hellman groups**

| Groups | Notes |
|--------|-------|
| **1, 2, 5** (legacy) | Deprecated; should no longer be used |
| **14, 15, 16** (standard) | Support 2048-, 3072-, and 4096-bit keys |
| **19, 20, 21, 24** (ECC) | Use Elliptic Curve Cryptography to speed up key generation |

## Exam Reminders
- VPN = private (encrypted) connection over a public network. Remote access = a user to the corporate network; site-to-site = network to network.
- Clientless SSL VPN uses only a browser; IPsec remote access needs client software.
- IPsec supports more applications and has stronger authentication; SSL is simpler to deploy.
- GRE carries multicast and routing updates but isn't secure, so combine it with IPsec (GRE over IPsec).
- DMVPN = hub-and-spoke with dynamic spoke-to-spoke tunnels (mGRE). VTI = IPsec on a virtual interface.
- AH = authentication and integrity only. ESP = encryption plus authentication.
- IPsec building blocks: protocol (AH/ESP), encryption (AES), hashing (SHA), authentication (PSK/RSA), DH group (14+ or ECC).
- DES, 3DES, MD5, and DH groups 1, 2, 5 are legacy; don't use them.
