# Module 16: Network Security Fundamentals

## 1. Security Threats and Vulnerabilities

### Types of Threats
Attacks on a network can cost time and money through damage or theft of important information or assets. Intruders can gain access through software vulnerabilities, hardware attacks, or by guessing a username and password. Intruders who get in by modifying software or exploiting software vulnerabilities are called **threat actors**.

Once a threat actor is in, four types of threats can arise:
1. Information theft
2. Data loss and manipulation
3. Identity theft
4. Disruption of service

### Types of Vulnerabilities
A **vulnerability** is the degree of weakness in a network or device. There are three primary kinds:

| Type | Examples |
|------|----------|
| **Technological** | TCP/IP protocol weaknesses, operating system weaknesses, network equipment weaknesses |
| **Configuration** | Unsecured user accounts, system accounts with easily guessed passwords, misconfigured internet services, unsecure default settings, misconfigured network equipment |
| **Security policy** | No written security policy, politics, lack of authentication continuity, logical access controls not applied, software/hardware installation and changes not following policy, no disaster recovery plan |

All three can leave a network or device open to attacks, including malicious code attacks and network attacks.

### Physical Security
If network resources can be physically compromised, a threat actor can deny their use. Four classes of physical threats:

| Class | Includes |
|-------|----------|
| **Hardware** | Physical damage to servers, routers, switches, the cabling plant, and workstations |
| **Environmental** | Temperature extremes (too hot or cold) and humidity extremes (too wet or dry) |
| **Electrical** | Voltage spikes, insufficient supply voltage (brownouts), unconditioned power (noise), total power loss |
| **Maintenance** | Poor handling of key electrical components (electrostatic discharge), lack of critical spare parts, poor cabling, poor labeling |

A good physical security plan must be created and carried out to address these issues.

## 2. Network Attacks

### Types of Malware
**Malware** (malicious software) is code designed to damage, disrupt, steal, or carry out illegitimate actions on data, hosts, or networks.

| Type | Description |
|------|-------------|
| **Virus** | Spreads by inserting a copy of itself into another program and becoming part of it. Moves from computer to computer, leaving infections as it goes |
| **Worm** | Like a virus, it replicates copies of itself and can cause the same damage. Unlike a virus, a worm is **standalone** software and needs no host file or human help to spread |
| **Trojan horse** | Harmful software that **looks legitimate**. Unlike viruses and worms, it does **not** reproduce by infecting other files or self-replicate. It spreads through user action, such as opening an email attachment or running a downloaded file |

### Categories of Network Attacks
1. **Reconnaissance:** discovering and mapping systems, services, or vulnerabilities. Threat actors can use tools like `nslookup` and `whois` to find the IP address space of an organization, then ping the public addresses to find the active ones.
2. **Access:** unauthorized manipulation of data, system access, or user privileges.
3. **Denial of Service (DoS):** disabling or corrupting networks, systems, or services.

### Access Attacks
Access attacks exploit known weaknesses in authentication, FTP, and web services to reach web accounts, confidential databases, and other sensitive information. Four types:

1. **Password attacks:** done with brute force, Trojan horses, and packet sniffers.
2. **Trust exploitation:** a threat actor uses unauthorized privileges to get into a system, possibly compromising the target.
3. **Port redirection:** a threat actor uses a compromised system as a base to attack other targets (e.g., SSH on port 22 to host A, then Telnet on port 23 from host A to trusted host B).
4. **Man-in-the-middle:** the threat actor sits between two legitimate parties to read or change the data passing between them.

### Denial of Service Attacks
- **DoS:** stops authorized people from using a service by consuming system resources. It interrupts communication, costs time and money, and is simple to carry out even for an unskilled attacker. Keeping operating systems and applications up to date helps prevent DoS attacks.
- **DDoS (Distributed DoS):** like DoS but from **multiple, coordinated sources**. The attacker builds a network of infected hosts called **zombies**; a network of zombies is a **botnet**. A **command and control (CnC)** program tells the botnet to carry out the attack.

## 3. Network Attack Mitigations

### Defense-in-Depth
Most organizations use **defense in depth** (a layered approach), with several devices and services working together:
1. VPN (Virtual Private Network)
2. ASA firewall
3. IPS (Intrusion Prevention System)
4. ESA/WSA (Email/Web Security Appliance)
5. AAA server

### Keeping Backups
Backing up device configurations and data is one of the most effective protections against data loss. Keep backups of configuration files and IOS images on an FTP or similar file server.

| Consideration | Description |
|---------------|-------------|
| **Frequency** | Back up regularly, as set in the security policy. Full backups are slow, so do monthly or weekly full backups with frequent partial backups of changed files |
| **Storage** | Move backups to an approved offsite location on a daily, weekly, or monthly rotation |
| **Security** | Protect backups with strong passwords required to restore data |
| **Validation** | Always validate backups to confirm data integrity, and validate restore procedures |

### Upgrade, Update, and Patch
- Keep antivirus software current as new malware appears.
- The most effective way to stop a worm is to download security updates from the OS vendor and **patch all vulnerable systems**.
- To manage critical patches, have all end systems download updates automatically.

### AAA (Authentication, Authorization, Accounting)
AAA ("triple A") is the main framework for access control on network devices:

| Service | Controls | Question |
|---------|----------|----------|
| **Authentication** | Who is allowed to access the network | "Who are you?" |
| **Authorization** | What users can do while on the network | "What can you do?" |
| **Accounting** | A record of what was done on the network | "What did you do?" |

### Firewalls
- Sit between two or more networks, control the traffic between them, and help prevent unauthorized access.
- Let traffic from the inside network go out and return, while denying outside traffic access to the inside network.
- Servers that outside users must reach sit in a special network called the **DMZ (demilitarized zone)**, which allows specific security policies for those hosts.

**Types of firewalls**

| Type | How it filters |
|------|----------------|
| **Packet filtering** | By IP or MAC addresses |
| **Application filtering** | By application type, using port numbers |
| **URL filtering** | By website URLs or keywords |
| **Stateful packet inspection (SPI)** | Incoming packets must be legitimate responses to requests from internal hosts; unsolicited packets are blocked unless specifically permitted. Can also filter out attack types like DoS |

### Endpoint Security
- An **endpoint** (host) is an individual computer system or device acting as a network client (laptops, desktops, servers, smartphones, tablets).
- Securing endpoints needs well-documented policies and trained employees.
- Policies often include antivirus software, host intrusion prevention, and network access control.

## 4. Device Security

### Cisco AutoSecure and Basic Hardening
1. Default OS security settings are often inadequate. Cisco routers can use **Cisco AutoSecure** to help secure the system.
2. Change default usernames and passwords immediately.
3. Restrict access to system resources to authorized people only.
4. Turn off and uninstall unnecessary services and applications.
5. Update software and install security patches before putting a device into service.

### Password Guidelines
1. Use at least 8 characters (10 or more is better).
2. Mix uppercase and lowercase letters, numbers, symbols, and spaces (if allowed).
3. Avoid repetition, dictionary words, letter/number sequences, usernames, names, or biographical information.
4. Deliberately misspell passwords (e.g., `Security` becomes `5ecur1ty`).
5. Change passwords often and don't write them down in obvious places.
6. **Passphrase:** Cisco routers recognize spaces after the first character, so a phrase of several words makes a password that is longer, easier to remember, and harder to guess.

### Additional Password Security Commands

| Purpose | Command |
|---------|---------|
| Encrypt plaintext passwords | `Router(config)# service password-encryption` |
| Set a minimum password length | `Router(config)# security password min-length 8` |
| Deter brute-force attacks | `Router(config)# login block-for 120 attempts 3 within 60` |
| Disconnect inactive sessions | `Router(config-line)# exec-timeout 5 30` |

### Enabling SSH
To set up SSH on a Cisco device:

```
Router(config)# hostname R1
R1(config)# ip domain-name example.com
R1(config)# crypto key generate rsa general-keys modulus 1024
R1(config)# username Admin secret Pass12345
R1(config)# line vty 0 4
R1(config-line)# login local
R1(config-line)# transport input ssh
```

| Step | Purpose |
|------|---------|
| `hostname` | Give the device a unique hostname |
| `ip domain-name` | Set the IP domain name |
| `crypto key generate rsa ...` | Generate the RSA key pair (recommended minimum modulus: 1024 bits) |
| `username ... secret ...` | Create a local database entry |
| `login local` | Authenticate against the local database |
| `transport input ssh` | Allow only SSH on the VTY lines |

### Disable Unused Services
Disable unused services to save system resources (CPU cycles and RAM) and prevent exploitation. Verify with:

| IOS version | Command |
|-------------|---------|
| IOS-XE | `Router# show ip ports all` |
| Pre-IOS-XE | `Router# show control-plane host open-ports` |

## Exam Reminders
- Three vulnerability types: technological, configuration, security policy.
- Four physical threat classes: hardware, environmental, electrical, maintenance.
- Virus needs a host file; worm is standalone and self-spreading; Trojan looks legitimate and needs user action.
- Attack categories: reconnaissance, access, DoS.
- DDoS uses a botnet of zombies controlled by a CnC program.
- AAA = authentication, authorization, accounting.
- Best defense against worms: patch systems.
- SSH setup order: hostname, domain name, RSA keys, local user, `login local`, `transport input ssh`.
