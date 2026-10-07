# Module 3: Network Security Concepts

## 1. Current State of Cybersecurity

### Security Terms and Definitions

| Term | Definition |
|------|------------|
| **Asset** | Anything of value to an organization (people, equipment, resources, data) |
| **Vulnerability** | A weakness in a system or design that could be exploited |
| **Threat** | A potential danger to an organization's assets, data, or network functionality |
| **Exploit** | The mechanism or tool used to take advantage of a vulnerability |
| **Mitigation** | Countermeasures that reduce the likelihood or severity of a potential threat |
| **Risk** | The likelihood that a threat exploits a vulnerability and harms the organization |

### Vectors of Network Attacks
- **Attack vector:** a path a threat actor uses to gain access to a network or host.
- **Internal vs. external threats:** internal threats can do more damage, because internal users have direct physical and logical access to the infrastructure.

### Data Loss Vectors (Exfiltration)
Data exfiltration is when data is lost, stolen, or leaked. Common vectors:
- **Email / social networking:** unencrypted messages captured in transit.
- **Unencrypted devices:** stolen devices that hold plain data.
- **Cloud storage devices:** sensitive data exposed by weak cloud security settings.
- **Removable media:** unauthorized transfers to USB drives.
- **Hard copy:** confidential paper documents left unshredded.
- **Improper access control:** compromised or weak passwords that give access.

## 2. Threat Actors

### Types of Hackers

| Type | Description |
|------|-------------|
| **White hat** | Ethical hackers who use their skills for good and legal purposes (e.g., finding vulnerabilities so they can be fixed) |
| **Gray hat** | People who do unethical things without malicious intent or personal gain (e.g., disclosing a breach after compromising a network) |
| **Black hat** | Criminals who compromise systems for personal gain or malicious intent |

### Modern Hacking Categories

| Category | Description |
|----------|-------------|
| **Script kiddies** | Inexperienced hackers who use existing tools and scripts without deep technical knowledge |
| **Vulnerability brokers** | Gray hats who find exploits to sell, or to report for bug bounties |
| **Hacktivists** | Hackers driven by political or social motives (e.g., Anonymous, Syrian Electronic Army) |
| **Cyber criminals** | Black hats in an underground economy that trades zero-day exploits, botnets, and stolen data |
| **State-sponsored hackers** | Advanced hackers backed by governments for espionage or sabotage (e.g., the Stuxnet malware that targeted Iran) |

## 3. Threat Actor Tools

### Common Penetration Testing / Hacking Tools

| Category | Examples |
|----------|----------|
| **Password crackers** | John the Ripper, Ophcrack, L0phtCrack, THC Hydra, Medusa |
| **Wireless hacking tools** | Aircrack-ng, Kismet, InSSIDer |
| **Network scanners / port scanners** | Nmap, SuperScan, Angry IP Scanner |
| **Packet crafting tools** | Hping, Scapy, Netcat, Yersinia |
| **Packet sniffers** | Wireshark, Tcpdump, Ettercap |
| **Fuzzers** (search for vulnerabilities) | Skipfish, Wapiti, W3af |
| **Vulnerability exploitation tools** | Metasploit, Core Impact, Sqlmap, Social Engineer Toolkit (SET) |
| **Vulnerability scanners** | Nessus, OpenVAS, Nipper, SAINT |

### General Attack Classifications
- **Eavesdropping / sniffer attack:** capturing and listening to unencrypted network traffic.
- **Data modification:** intercepting packets and changing their data before forwarding them.
- **IP address spoofing:** building IP packets with a false source address.
- **Man-in-the-middle (MITM):** placing an attack host transparently between a sender and receiver.
- **Compromised-key attack:** getting hold of secret keys to decrypt secured communications.

## 4. Malware

### Primary Malware Types

| Type | Description |
|------|-------------|
| **Virus** | Malicious code attached to legitimate files or programs. **Needs human action to spread.** Types: boot sector, firmware, macro, program, and script viruses |
| **Worm** | A self-replicating program that spreads **automatically, without user action**, across network links |
| **Trojan horse** | A program that looks useful or legitimate but carries hidden malicious code. Types: remote-access, keylogger, proxy, DoS, destructive, and security software disabler |

### Other Malware Forms
- **Adware:** shows unsolicited pop-up ads or redirects the browser.
- **Ransomware:** encrypts the victim's files and demands payment (e.g., Bitcoin) for the decryption key.
- **Rootkits:** install at the administrator/kernel level and hide by changing OS commands. Very hard to detect.
- **Spyware:** secretly tracks the user's browsing habits and sensitive personal credentials.

## 5. Common Network Attacks

### Three Main Attack Categories
1. **Reconnaissance attacks:** unauthorized discovery and mapping of systems, services, and vulnerabilities before an attack.
   - *Phases:* information queries (Google, whois) → ping sweeps → port scans (Nmap) → vulnerability scans (Nessus).
2. **Access attacks:** exploiting known weaknesses to get in, retrieve data, or escalate privileges. Include password attacks, spoofing, trust exploitation, port redirection, and buffer overflows.
3. **Denial of service (DoS) attacks:** interrupting or stopping network services for legitimate users. **DDoS** comes from many coordinated botnet nodes.

### Social Engineering Techniques

| Technique | Description |
|-----------|-------------|
| **Pretexting** | Making up a fake scenario that needs the victim's personal information to "verify" something |
| **Phishing / spear phishing** | Mass fraudulent emails vs. targeted emails, designed to trick victims into sharing information or installing malware |
| **Something for something (quid pro quo)** | Asking for information in exchange for a gift or favor |
| **Baiting** | Leaving infected media (e.g., a USB drive) in a public place |
| **Tailgating** | Following an authorized person closely into a secure physical area |
| **Shoulder surfing / dumpster diving** | Watching screens and passwords over someone's shoulder, or searching the trash for papers |

## 6. IP Vulnerabilities and Threats

### ICMP Attacks
Threat actors use ICMP for reconnaissance (OS fingerprinting, network mapping) and for DoS attacks.
- **ICMP messages that get exploited:** echo request/reply, unreachable, mask reply, redirects (lure traffic for a MITM), and router discovery (route table injection).
- **Smurf attack:** an ICMP echo request with the **victim's spoofed source IP** is sent to a subnet broadcast address, so the victim is flooded with replies.

### Address Spoofing Categories
- **Non-blind spoofing:** the attacker can see the traffic between the targets, which lets them inspect state and hijack sessions.
- **Blind spoofing:** the attacker can't see the traffic flow; used mainly in DoS attacks.

## 7. TCP and UDP Vulnerabilities

### TCP Control Bits (Flags)
**URG** (urgent), **ACK** (acknowledgment), **PSH** (push), **RST** (reset), **SYN** (synchronize), **FIN** (finish).

### TCP Attacks

| Attack | Description |
|--------|-------------|
| **TCP SYN flood** | The attacker sends repeated SYN requests and never answers the SYN-ACKs, filling the server's queue with half-open connections |
| **TCP reset attack** | A spoofed packet with the `RST` flag set abruptly breaks an established connection |
| **TCP session hijacking** | The attacker spoofs a client's IP address and predicts sequence numbers to take over an authenticated connection |

### UDP Attacks
- UDP has no connection state and no encryption by default.
- **UDP flood:** floods random ports on a target with UDP packets, forcing the host to answer with a stream of ICMP Port Unreachable messages.

## 8. IP Services Vulnerabilities

### ARP Cache Poisoning
- Exploits unsolicited **gratuitous ARP** replies, which any host can send.
- The attacker broadcasts false IP-to-MAC associations, so devices point their gateway entry at the attacker's MAC address, setting up a MITM attack.

### DNS Attacks

| Attack | Description |
|--------|-------------|
| **DNS open resolvers** | Vulnerable to DNS cache poisoning (redirecting to fake sites) and DNS amplification/reflection attacks |
| **DNS stealth** (fast flux, double IP flux, DGA) | Rapidly changing IP mappings, name servers, or domain-generation algorithms to hide command-and-control (C&C) servers |
| **Domain shadowing** | Hijacking domain credentials to quietly create hidden subdomains that point to malicious servers |
| **DNS tunneling** | Hiding non-DNS commands or stolen data inside the lower-level labels of domain queries, to get past firewalls |

### DHCP Attacks
**DHCP spoofing (rogue DHCP server):** an unauthorized server gives out false configuration:
- *Wrong gateway:* intercepts data traffic (MITM).
- *Wrong DNS server:* redirects users to malicious websites.
- *Wrong IP address:* causes a DoS.

## 9. Network Security Best Practices

### The CIA Triad

| Principle | Meaning | How it's ensured |
|-----------|---------|------------------|
| **Confidentiality** | Only authorized users can access the data | Encryption (e.g., AES) |
| **Integrity** | Data is protected from unauthorized changes | Hashes (e.g., SHA) |
| **Availability** | Network services are reliably accessible | Redundancy |

### Defense-in-Depth and Appliance Roles
- **Firewalls:** enforce access policies between networks.
- **IDS vs. IPS:**
  - *IDS (detection):* watches a copy of the traffic and alerts on matching signatures.
  - *IPS (prevention):* sits **in-line**, checks traffic, and actively drops bad packets.
- **Cisco ESA (Email Security Appliance):** filters SMTP traffic; pulls intelligence from Cisco Talos every 3 to 5 minutes.
- **Cisco WSA (Web Security Appliance):** does URL filtering, blacklisting, categorization, and web traffic decryption.

## 10. Cryptography

### Four Elements of Secure Communications

| Element | Guarantees | Provided by |
|---------|------------|-------------|
| **Data integrity** | The message wasn't altered | MD5, SHA |
| **Origin authentication** | The source is legitimate | HMAC |
| **Data confidentiality** | No unauthorized reading | Symmetric and asymmetric encryption |
| **Non-repudiation** | The sender can't deny sending the message | Digital signatures |

### Hash Functions and HMAC
- **MD5:** 128-bit digest (legacy).
- **SHA-1:** 160-bit digest (legacy).
- **SHA-2:** the next generation (SHA-224, SHA-256, SHA-384, SHA-512).
- **HMAC:** combines a cryptographic hash function with a secret key, to ensure both **integrity and origin authentication**.

### Encryption Algorithms Comparison

| Parameter | Symmetric encryption | Asymmetric encryption |
|-----------|----------------------|-----------------------|
| **Keys** | The **same** pre-shared secret key | A pair of **public and private** keys |
| **Key length** | Short (40 to 256 bits) | Long (512 to 4096 bits) |
| **Performance** | Fast, low CPU impact; used for bulk data and VPNs | Slow, computationally heavy; used for key exchange |
| **Examples** | DES, 3DES, **AES**, SEAL, RC4 | **DH**, RSA, DSS/DSA, ElGamal, ECC |

**Diffie-Hellman (DH):** an asymmetric algorithm that lets two parties agree on a shared secret key over an insecure channel without sending the key itself. Used in IPsec VPNs, SSL/TLS, and SSH.

## Exam Reminders
- Terms: vulnerability = weakness, threat = danger, exploit = the tool, risk = likelihood of impact, mitigation = countermeasure.
- Virus needs a human; worm spreads on its own; Trojan looks legitimate.
- Attack categories: reconnaissance, access, DoS (DDoS uses a botnet).
- Social engineering: pretexting, phishing/spear phishing, quid pro quo, baiting, tailgating, shoulder surfing, dumpster diving.
- TCP SYN flood = half-open connections; UDP flood = ICMP port unreachable replies; smurf = spoofed ICMP to a broadcast.
- CIA triad: confidentiality (encryption), integrity (hashes), availability (redundancy).
- IDS detects and alerts; IPS is in-line and blocks.
- Symmetric = one shared key, fast (AES). Asymmetric = key pair, slow (RSA, DH).
- HMAC = hash + secret key = integrity and origin authentication.
