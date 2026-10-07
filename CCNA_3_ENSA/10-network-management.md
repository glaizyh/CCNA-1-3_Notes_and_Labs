# Module 10: Network Management

## 1. Device Discovery with CDP

### CDP Overview
- **Protocol type:** a Cisco-proprietary Layer 2 protocol used to discover directly connected Cisco devices.
- **Characteristics:** media and protocol independent; runs on routers, switches, and access servers.
- **Operation:** sends periodic Layer 2 advertisements to connected neighbors, sharing the device name, interface types and numbers, and capabilities.

### Configuring and Verifying CDP
- **Default state:** enabled globally on Cisco devices.

| Purpose | Command |
|---------|---------|
| Enable CDP globally on all supported interfaces | `cdp run` |
| Disable CDP globally on the whole device | `no cdp run` |
| Enable CDP on one interface | `cdp enable` |
| Disable CDP on one interface (stops sending CDP advertisements out of it) | `no cdp enable` |

| Verification command | Shows |
|----------------------|-------|
| `show cdp` | Global CDP status |
| `show cdp interface` | CDP status on all active interfaces |
| `show cdp neighbors` | A summary list of directly connected neighbors |
| `show cdp neighbors detail` | Detailed neighbor information: IPv4/IPv6 addresses, IOS version, platform, and connected ports |

## 2. Device Discovery with LLDP

### LLDP Overview
- **Protocol type:** a vendor-neutral Layer 2 discovery protocol (IEEE 802.1AB), similar to CDP.
- **Operation:** advertises identity and capabilities to physically connected neighbors in multi-vendor environments.

### Configuring and Verifying LLDP

| Purpose | Command |
|---------|---------|
| Enable LLDP globally | `lldp run` |
| Disable LLDP globally | `no lldp run` |
| Send LLDP packets on an interface | `lldp transmit` |
| Receive LLDP packets on an interface | `lldp receive` |

The interface commands must be configured separately, to send and receive LLDP packets.

| Verification command | Shows |
|----------------------|-------|
| `show lldp` | Global LLDP timers and operational status |
| `show lldp neighbors` | Basic summary information about discovered neighbors |
| `show lldp neighbors detail` | Detailed neighbor information (IP address, IOS version, capabilities) |

## 3. NTP (Network Time Protocol)

### Time Synchronization and the Stratum Hierarchy
- **Purpose:** keeps the software clocks of network devices in sync, so event logs are accurate and events can be lined up when troubleshooting.
- **Transport:** uses **UDP port 123** (defined in RFC 1305).
- **Stratum levels:** a hierarchy that shows how many hops a device is from an authoritative time source.

| Stratum | Description |
|---------|-------------|
| **0** | High-precision atomic or GPS time sources (authoritative) |
| **1** | Servers connected directly to stratum 0 sources |
| **2** | Devices synchronized over the network with stratum 1 servers (clients of stratum 1 and servers for stratum 3) |
| **16** | The lowest level: an unsynchronized device. The maximum hop count is 15 |

### NTP Configuration and Verification
- **Global command:** `ntp server [ip-address]` makes the device synchronize its time with a remote NTP server.

| Verification command | Shows |
|----------------------|-------|
| `show clock detail` | The time and the current time source (e.g., `Time source is NTP` vs. `user configuration`) |
| `show ntp status` | Synchronization status, current stratum level, and the reference clock IP |
| `show ntp associations` | Details about the peered or configured NTP servers (delay, offset, dispersion) |

## 4. SNMP (Simple Network Management Protocol)

### SNMP Elements and Architecture
- **SNMP manager:** part of a Network Management System (NMS); collects data and manages agent configurations.
- **SNMP agent:** software on managed network devices (routers, switches) that answers queries and reports events.
- **Management Information Base (MIB):** a hierarchical structure that stores operational data, statistics, and variables on the managed device.

### SNMP Operations and UDP Ports

| Port | Use |
|------|-----|
| **UDP 161** | The SNMP manager uses it to query or poll SNMP agents |
| **UDP 162** | SNMP agents use it to send unsolicited alerts (**traps**) to the manager |

| Request type | Purpose |
|--------------|---------|
| `get-request` | Retrieves the value of a specific variable from the MIB |
| `get-next-request` | Searches the MIB table in order and retrieves the next variable |
| `get-bulk-request` | Retrieves large blocks of data or tables in one request (SNMPv2c or later) |
| `get-response` | Returns the requested values to the NMS |
| `set-request` | Changes a MIB variable's value, or triggers an action on the agent |
| `trap` | An unsolicited notification an agent sends when a specific event happens (e.g., an interface failure) |

### SNMP Versions and Security

| Version | Description |
|---------|-------------|
| **SNMPv1** | Legacy standard. Uses plaintext community strings for basic authentication. Insecure |
| **SNMPv2c** | Community string based. Adds bulk retrieval (`get-bulk`) and more detailed error messages |
| **SNMPv3** | Secure. Provides user authentication (HMAC-MD5 or HMAC-SHA) and data encryption (DES, 3DES, AES) |

**Community string types**
- `read-only (ro)`: lets you view MIB objects but not change them.
- `read-write (rw)`: lets you view and change MIB variables.

### Object Identifiers (OID)
- **Definition:** unique numeric strings that identify specific MIB variables arranged in a hierarchical tree.
- **Cisco OID branch:** `.1.3.6.1.4.1.9` (`iso.org.dod.internet.private.enterprises.cisco`).

## 5. Syslog

### Overview and Operation
- **Transport:** runs over **UDP port 514** to send event messages across IP networks.
- **Functions:** collects system logs for monitoring and troubleshooting, controls which severity levels are captured, and sends log messages to chosen destinations.
- **Log destinations:**
  - The logging buffer (RAM in the device)
  - The console line
  - The terminal lines (VTY)
  - An external syslog server

### Syslog Severity Levels
Levels run from 0 (most severe) to 7 (least severe, debugging):

| Level | Name | Explanation |
|-------|------|-------------|
| **0** | Emergency | System unusable |
| **1** | Alert | Immediate action needed |
| **2** | Critical | Critical condition |
| **3** | Error | Error condition |
| **4** | Warning | Warning condition |
| **5** | Notification | Normal but significant condition |
| **6** | Informational | Informational message |
| **7** | Debugging | Debugging message |

### Message Format and Timestamps
- **Format:** `%facility-severity-MNEMONIC: description`
  - Example: `%LINK-3-UPDOWN: Interface GigabitEthernet0/0/0, changed state to down`
- **Timestamp command:** `service timestamps log datetime` makes log messages include the full date and time, which helps with auditing.

## 6. Router and Switch File Maintenance

### Cisco IOS File System (IFS) Commands

| Command | Purpose |
|---------|---------|
| `show file systems` | Lists the available file systems (flash, nvram, system, tftp, usb) |
| `dir` | Lists the contents of the current default file system (usually `bootflash:` or `flash:`) |
| `cd [file-system:]` | Changes the working directory (e.g., `cd nvram:`) |
| `pwd` | Shows the present working directory |

### Configuration Backups and Restorations

| Method | Back up | Restore |
|--------|---------|---------|
| **Text file / terminal capture** (Tera Term) | Log the session output to a file and run `show running-config` | Open the saved text file and use `Send file` in the terminal software |
| **TFTP server** | `copy running-config tftp:` | `copy tftp: running-config` (into the active RAM configuration) |
| **USB flash drive** | `copy running-config usbflash0:` | `copy usbflash0:[filename] running-config` |

### Password Recovery Procedure
1. Connect to the console and restart the device. Enter the break sequence to reach **ROMMON** mode.
2. Change the configuration register to **`0x2142`** (`confreg 0x2142`), so the router ignores `startup-config` at boot.
3. Type `reset` to reboot the router.
4. Copy the startup configuration into the running configuration: `copy startup-config running-config`.
5. Enter global configuration mode and set the new passwords (e.g., `enable secret`).
6. Set the configuration register back to the default **`0x2102`**: `config-register 0x2102`.
7. Save the running configuration to the startup configuration (`copy running-config startup-config`) and reload.

## 7. IOS Image Management

### Backing Up and Upgrading IOS Images with TFTP
1. Test network connectivity by pinging the TFTP server.
2. Check the free space in flash with `show flash:`.
3. Transfer the IOS image file:
   - **Back up:** `copy flash: tftp:`
   - **Upgrade:** `copy tftp: flash:`

### Boot Order (`boot system`)
- **Global command:** `boot system flash0:[image-filename.bin]` tells the router's bootstrap code which IOS image to load at startup.
- **Default behavior:** with no `boot system` command, the router loads the first valid IOS image it finds in flash.

## Exam Reminders
- CDP = Cisco proprietary; LLDP = IEEE 802.1AB, vendor neutral. Disable CDP with `no cdp run`; enable LLDP with `lldp run`.
- NTP uses UDP 123. Stratum 0 = atomic/GPS clock, stratum 16 = unsynchronized. Configure with `ntp server <ip>`; verify with `show ntp status`.
- SNMP: manager polls on UDP 161; agent sends traps on UDP 162. SNMPv3 is the secure version (authentication and encryption).
- Syslog uses UDP 514. Levels 0 (emergency) to 7 (debugging); lower numbers are more severe.
- Back up the config with `copy running-config tftp:`; restore with `copy tftp: running-config`.
- Password recovery: ROMMON, `confreg 0x2142`, `reset`, copy startup to running, change the password, set the register back to `0x2102`, save.
- IOS upgrade: ping the TFTP server, check `show flash:`, then `copy tftp: flash:`.
