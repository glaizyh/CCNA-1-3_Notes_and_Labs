# Module 10: LAN Security Concepts

## 1. Endpoint Security

### Network Attacks and Security Devices

**Common attacks**
- **DDoS:** a coordinated attack from many devices (zombies) to slow down or stop access to resources.
- **Data breach:** compromising servers or hosts to steal confidential information.
- **Malware:** infection by malicious software, such as ransomware like *WannaCry*, which encrypts data until a ransom is paid.

**Perimeter security devices**

| Device | Role |
|--------|------|
| **VPN-enabled router** | Gives remote users a secure connection across public networks |
| **Next-Generation Firewall (NGFW)** | Stateful packet inspection, application control, NGIPS, Advanced Malware Protection (AMP), and URL filtering |
| **Network Access Control (NAC)** | Includes AAA services and manages access policies (e.g., Cisco Identity Services Engine, ISE) |

### Endpoint Protection and Appliances
- **Endpoints:** laptops, desktops, servers, IP phones, and employee-owned devices. They are exposed to malware through email and web browsing.
- **Best protection:** a combination of NAC, AMP software, an Email Security Appliance (ESA), and a Web Security Appliance (WSA).

| Appliance | What it does |
|-----------|--------------|
| **Cisco ESA** (Email Security Appliance) | Monitors SMTP. Pulls real-time threat intelligence from Cisco Talos every **3 to 5 minutes**. Blocks known threats, remediates stealth malware, discards bad links, blocks newly infected sites, and encrypts outgoing email content |
| **Cisco WSA** (Web Security Appliance) | Mitigates web-based threats and controls users' internet traffic. Combines AMP, application visibility, acceptable use policy controls, and reporting. Does URL blacklisting, URL filtering, URL categorization, web application filtering, and traffic encryption/decryption |

## 2. Access Control

### Remote Access and Passwords
- **Local password authentication:** simple login/password combinations on the console, vty lines, and aux ports.
- **SSH access:** needs a username and password, checked locally or on a central server.
- **Local database limits:** it doesn't scale across many devices, and there's no fallback authentication method.

### AAA Framework
AAA stands for **Authentication, Authorization, and Accounting**.

| Component | Question | Description |
|-----------|----------|-------------|
| **Authentication** | Who are you? | Controls who can access the network |
| **Authorization** | What can you do? | Automatically applies user attributes to enforce privileges and restrictions after authentication |
| **Accounting** | What did you do? | Collects and reports usage data (start/stop times, EXEC and configuration commands, packets, bytes) for auditing and troubleshooting |

**Authentication methods**
- **Local AAA:** usernames and passwords stored on the network device. Best for small networks.
- **Server-based AAA:** the device talks to a central AAA server using **RADIUS** or **TACACS+**. Best for networks with many devices.

### IEEE 802.1X Standard
A port-based access control and authentication protocol that stops unauthorized workstations from connecting to a LAN. It has three roles:

| Role | Description |
|------|-------------|
| **Client (supplicant)** | A workstation running 802.1X-compliant client software |
| **Switch (authenticator)** | Acts as the go-between: asks the client for its identity and checks it with the server |
| **Authentication server** | Validates the client's identity and tells the switch whether access is allowed |

## 3. Layer 2 Security Threats

### Layer 2 Vulnerabilities
- A Layer 2 failure undermines the security of the higher layers (3 to 7).
- If threat actors capture Layer 2 frames, they can reach sensitive internal data.

### Attack Categories and Mitigation

| Category | Attack examples | Main mitigation |
|----------|-----------------|-----------------|
| **MAC table attacks** | MAC address flooding | **Port security** |
| **VLAN attacks** | VLAN hopping, double tagging | Trunk security practices |
| **DHCP attacks** | DHCP starvation, DHCP spoofing | **DHCP snooping** |
| **ARP attacks** | ARP spoofing, ARP poisoning | **Dynamic ARP Inspection (DAI)** |
| **Address spoofing** | MAC and IP address spoofing | **IP Source Guard (IPSG)** |
| **STP attacks** | STP manipulation | **BPDU Guard** |

### Management Protocol Best Practices
- Use secure management protocols: SSH, SCP, SFTP, SSL/TLS.
- Use out-of-band management networks and a dedicated management VLAN.
- Use ACLs to filter unwanted access.

## 4. MAC Address Table Attack

### MAC Table Flooding
- **How it works:** tools like `macof` flood a switch with fake source MAC addresses (up to about 8,000 bogus frames per second).
- **Result:** when the switch's memory fills up, it fails open. It treats frames as unknown unicasts and floods traffic out all ports in the local VLAN.
- **Impact:** the attacker can sniff all traffic on the local LAN/VLAN, and the problem can spill over to other connected Layer 2 switches.
- **Mitigation:** configure **port security** to limit the MAC addresses allowed on each port.

## 5. LAN Attacks

### VLAN Hopping and Double Tagging
**VLAN hopping:** the attacker sets up a host to spoof 802.1Q and DTP signaling, forms a trunk with the switch, and gains access to all VLANs.

**VLAN double tagging**
- The attacker puts a hidden 802.1Q tag inside a frame that already carries the native VLAN tag.
- The first switch removes the native tag. The second switch reads the inner tag and forwards the frame to the target VLAN.
- It is **one-way** only, and works only when the attacker is in the same VLAN as the trunk's native VLAN.

**Mitigation**
1. Disable trunking on all access ports.
2. Disable auto-trunking (DTP) on trunk links; enable trunks manually.
3. Use the native VLAN **only** for trunk links.

### DHCP Messages and Attacks

**DHCP message exchange**

| Step | Message | Type |
|------|---------|------|
| 1 | DHCPDISCOVER | Client broadcast |
| 2 | DHCPOFFER | Server unicast |
| 3 | DHCPREQUEST | Client broadcast |
| 4 | DHCPACK | Server unicast |

**DHCP starvation**
- Uses tools (e.g., Gobbler) to request every available IP address with bogus MAC addresses.
- Creates a denial of service (DoS) for new clients.

**DHCP spoofing**
- A rogue DHCP server hands out false IP information:
  - *Wrong default gateway:* intercepts data (man-in-the-middle).
  - *Wrong DNS server:* redirects users to malicious sites.
  - *Wrong IP address:* causes a DoS.

**Mitigation:** **DHCP snooping**.

### ARP Attacks and Address Spoofing
- **Gratuitous ARP exploitation:** the attacker sends unsolicited ARP replies with spoofed MAC addresses.
- **ARP spoofing / poisoning:** the attacker ties their own MAC address to the default gateway's IP address to run a man-in-the-middle attack.
- **MAC spoofing:** the attacker changes their host's MAC address to match a target host; the switch updates its table and wrongly forwards frames.
- **IP spoofing:** the attacker hijacks a valid IP address on the subnet.

**Mitigations**
- ARP attacks: **Dynamic ARP Inspection (DAI)**.
- Address spoofing: **IP Source Guard (IPSG)**.

### STP Manipulation and CDP Reconnaissance
- **STP attack:** the attacker sends BPDUs with a low bridge priority to become the **root bridge** and redirect network traffic.
  - Mitigation: **BPDU Guard** on access ports.
- **CDP reconnaissance:** CDP sends periodic, unencrypted Layer 2 broadcasts that include the IP address, IOS version, platform, capabilities, and native VLAN.

| Purpose | Command |
|---------|---------|
| Disable CDP globally | `no cdp run` |
| Disable CDP on an interface | `no cdp enable` |
| Disable LLDP globally | `no lldp run` |
| Disable LLDP on an interface | `no lldp transmit` and `no lldp receive` |

## Exam Reminders
- Attack to mitigation: MAC flooding = port security; DHCP attacks = DHCP snooping; ARP attacks = DAI; address spoofing = IPSG; STP attacks = BPDU Guard.
- VLAN hopping fixes: no trunking on access ports, turn DTP off, native VLAN only for trunks.
- `macof` floods MAC tables; Gobbler starves DHCP.
- AAA: authentication (who), authorization (what), accounting (what you did). RADIUS or TACACS+ for server-based AAA.
- 802.1X roles: supplicant (client), authenticator (switch), authentication server.
- Disable CDP with `no cdp run` and LLDP with `no lldp run`.
- Use SSH (never Telnet) and a dedicated management VLAN.
