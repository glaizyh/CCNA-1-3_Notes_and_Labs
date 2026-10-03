# Module 1: Basic Device Configuration

## 1. Switch Boot Sequence and Initial Setup

### 5-Step Boot Sequence
1. **POST (Power-On Self-Test):** loaded from ROM; tests the CPU subsystem, DRAM, and the flash file system.
2. **Boot loader:** a small program stored in ROM, run right after POST.
3. **CPU initialization:** the boot loader initializes CPU registers, which control physical memory mapping, speed, and quantity.
4. **Flash file system initialization:** the boot loader initializes the system board flash file system.
5. **IOS loading:** the boot loader locates and loads the default IOS image into memory, then hands over control.

### Boot Commands
| Purpose | Command |
|---------|---------|
| Set the BOOT variable | `boot system flash:/[folder]/[filename.bin]` |
| Verify the current boot file | `show boot` |

## 2. Switch LED Indicators and System Recovery

### LED Modes and Indicators

| LED | Shows |
|-----|-------|
| **SYST** (System) | System power and operational status |
| **RPS** (Redundant Power Supply) | RPS status |
| **STAT** (Port status) | Port status mode (default) |
| **DUPLX** (Port duplex) | Port duplex mode |
| **SPEED** (Port speed) | Port speed mode |
| **PoE** (Power over Ethernet) | PoE status, if supported |

The **Mode button** cycles through the STAT, DUPLX, SPEED, and PoE modes.

### System Crash / Password Recovery
1. Connect a PC to the switch console port and open a terminal emulator.
2. Unplug the switch power cord.
3. Plug the power cord back in, and press and hold the **Mode** button within 15 seconds, while the System LED flashes green.
4. Keep holding until the System LED turns amber and then solid green, then release the button.
5. The terminal shows the `switch:` prompt.

Useful command at that prompt: `dir` (lists the flash directory).

## 3. Switch Management Access (SVI Configuration)

### Basic SVI Setup
- **Default VLAN:** VLAN 1. Best practice is to use a non-default VLAN for management, such as VLAN 99.
- **IPv6 note:** a Catalyst 2960 running IOS 15.0 needs `sdm prefer dual-ipv4-and-ipv6 default` before IPv6 can be set up.

### Configuration Commands
```
S1# configure terminal
S1(config)# interface vlan 99
S1(config-if)# ip address 172.17.99.11 255.255.255.0
S1(config-if)# ipv6 address 2001:db8:acad:99::1/64
S1(config-if)# no shutdown
S1(config-if)# exit
S1(config)# ip default-gateway 172.17.99.1
S1(config)# end
S1# copy running-config startup-config
```

### Verification Commands
```
show ip interface brief
show ipv6 interface brief
```

## 4. Switch Port Configuration and Troubleshooting

### Duplex and Speed Settings
- **Full-duplex:** simultaneous two-way transmission; no collisions; requires microsegmentation.
- **Half-duplex:** one direction at a time; prone to collisions.
- **Default:** `auto` speed and `auto` duplex on Catalyst 2960/3560 switches.
- **Gigabit Ethernet and fiber** require full-duplex operation.

### Port Configuration Commands
```
S1# configure terminal
S1(config)# interface FastEthernet 0/1
S1(config-if)# duplex full
S1(config-if)# speed 100
S1(config-if)# mdix auto
S1(config-if)# end
```

- **Auto-MDIX:** detects the cable type (straight-through or crossover). It requires speed and duplex to be set to `auto`.
- **Verify Auto-MDIX:** `show controllers ethernet-controller | include Auto-MDIX`

### Port Interface Verification and Errors
`show interfaces [interface-id]` checks Layer 1/Layer 2 status, speed, duplex, and error counts.

| Status | Likely cause |
|--------|--------------|
| **Interface up / line protocol down** | Encapsulation mismatch, remote end error-disabled, or a hardware fault |
| **Interface down / line protocol down** | Cable unplugged, or the remote end administratively down |
| **Administratively down** | Manually disabled with the `shutdown` command |

**Common errors**

| Error | Meaning and cause |
|-------|-------------------|
| **Runts** | Frames smaller than 64 bytes (faulty NICs or collisions) |
| **Giants** | Frames larger than 1,518 bytes |
| **CRC errors** | Checksum mismatches (cable noise, interference, or bad connectors) |
| **Late collisions** | Collisions after 512 bits have been sent (cable too long, or duplex mismatch) |

## 5. Secure Remote Access (SSH vs. Telnet)

### Telnet vs. SSH

| | Telnet | SSH |
|--|--------|-----|
| Port | TCP 23 | TCP 22 |
| Security | Insecure; sends data and credentials in plaintext | Secure; encrypts credentials and session data |

- **IOS requirement:** the software image filename must contain **`k9`** to support encryption.

### SSH Configuration Steps
```
S1# show version
S1# configure terminal
S1(config)# ip domain-name example.com
S1(config)# crypto key generate rsa
S1(config)# username admin secret ccna
S1(config)# line vty 0 15
S1(config-line)# transport input ssh
S1(config-line)# login local
S1(config-line)# exit
S1(config)# ip ssh version 2
```

`show version` is the first step because it shows whether the IOS image supports encryption (`k9`).

### SSH Verification Commands
| Command | Shows |
|---------|-------|
| `show ip ssh` | SSH version and parameters |
| `show ssh` | Active SSH connections |

## 6. Basic Router Configuration

### Initial Router Setup
```
Router# configure terminal
Router(config)# hostname R1
R1(config)# enable secret class
R1(config)# line console 0
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# exit
R1(config)# line vty 0 4
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# exit
R1(config)# service password-encryption
R1(config)# banner motd $ Authorized Access Only! $
R1(config)# end
R1# copy running-config startup-config
```

### Interface Setup Commands
```
R1(config)# interface gigabitethernet 0/0/0
R1(config-if)# description Link to LAN 1
R1(config-if)# ip address 192.168.10.1 255.255.255.0
R1(config-if)# ipv6 address 2001:db8:acad:1::1/64
R1(config-if)# no shutdown
R1(config-if)# exit
```

### IPv4 Loopback Interface
- An internal, logical software interface.
- It stays **up** as long as the router is running, which makes it useful for testing and management.

```
R1(config)# interface loopback 0
R1(config-if)# ip address 10.0.0.1 255.255.255.255
```

## 7. Verification and Helpful CLI Commands

### Essential Verification Commands
```
show ip interface brief
show ipv6 interface brief
show running-config interface [interface-id]
show ip route
show ipv6 route
```

Routing table codes:
- **C (connected):** a directly connected network.
- **L (local):** a local host route assigned to the interface (/32 for IPv4, /128 for IPv6).

### Output Filtering
Add a pipe (`|`) after a `show` command:

| Filter | Shows |
|--------|-------|
| `section` | The whole section that starts with the expression |
| `include` | Lines that match the expression |
| `exclude` | Everything except lines that match the expression |
| `begin` | Output starting at the first line that matches the expression |

Example: `show running-config | section line vty`

### History Buffer Settings

| Key / command | Action |
|---------------|--------|
| `Ctrl+P` / Up arrow | Recall the previous command |
| `Ctrl+N` / Down arrow | Recall a newer command |
| `show history` | Show the captured history |
| `terminal history size [number]` | Change the buffer size for the current session |

The default buffer holds the last **10** commands.

## Exam Reminders
- Boot order: POST, boot loader, CPU init, flash file system init, IOS load.
- Mode button recovery: hold Mode while the System LED flashes green; get the `switch:` prompt.
- Use a non-default VLAN (e.g., VLAN 99) for the management SVI, plus `ip default-gateway`.
- Auto-MDIX needs speed and duplex set to `auto`.
- `k9` in the IOS filename = supports SSH encryption.
- Runts < 64 bytes; giants > 1,518 bytes; CRC = bad cable or noise; late collisions = duplex mismatch or long cable.
- `L` routes are /32 (IPv4) or /128 (IPv6).
