# Module 13: WLAN Configuration

## 1. Remote Site WLAN Configuration

### Small Office / Home Office (SOHO) Routers
- **Integrated device:** a SOHO router usually includes a switch for wired clients, a WAN port for the internet connection, and wireless components for wireless clients.
- **Integrated services:** typically WLAN security, DHCP, NAT, Quality of Service (QoS), and other features depending on the model.
- **Service provider role:** the cable or DSL modem is normally configured by the service provider.

### Router Access and Initial Setup
- **Security priority:** default IP addresses, usernames, and passwords (often `admin`) are easy to find online, so changing them right away is a top priority.

**Basic setup steps**
1. Log in with a web browser, using the default IP address.
2. Change the default administrative password.
3. Change the default DHCP IPv4 address pool, then log back in with the new IP address.
4. Configure the basic wireless settings:
   - Network mode (802.11 standard)
   - SSID
   - Channel (avoid overlap)
   - Security mode (Open, WPA, WPA2 Personal/Enterprise)
   - Passphrase

### Network Expansion and Traffic Management
- **Wireless mesh network (WMN):** extends range beyond about 45 meters indoors and 90 meters outdoors by adding access points with matching settings, on different non-overlapping channels to avoid interference.
- **Network Address Translation (NAT):** translates private (local) IPv4 addresses to a public (global) address, so many hosts can share one public IP by tracking source port numbers.
- **Quality of Service (QoS):** gives priority to time-sensitive traffic (voice and video) over ordinary traffic (email and web browsing).

| Feature | Description |
|---------|-------------|
| **Port forwarding** | Directs traffic between devices on separate networks, for specific opened ports |
| **Port triggering** | Temporarily forwards inbound data to a specific device, only after an outbound request on a designated port range triggers it |

## 2. Configure a Basic WLAN on the WLC

### Controller Topology and Architecture
- **Lightweight access points (LAPs):** controller-based APs that need no initial manual configuration.
- **LWAPP / CAPWAP:** the protocols LAPs use to talk directly to the Wireless LAN Controller (WLC).
- **Scalability:** configuration is automatic and management is centralized as new APs are added.
- **Trunking and interfaces:** the WLC's physical ports act as trunk ports, carrying traffic from multiple VLANs/WLANs over virtual interfaces.

### WLC Dashboard and Information
- **Network Summary dashboard:** a central overview of configured WLANs, associated APs, active clients, rogue APs/clients, and system performance.
- **AP view:** detailed system information (IP, MAC, switch port connectivity through CDP, spatial streams, operating system, and performance).
- **Advanced view:** click **Advanced** in the upper right corner to reach all the WLC's feature settings.

### Basic WLAN Creation Steps
1. **Create the WLAN:** WLANs tab → Create New → set the profile name and SSID.
2. **Apply and enable:** set the status to *Enabled* on the General tab.
3. **Select the interface:** choose the management or VLAN interface that will carry the WLAN's traffic.
4. **Secure the WLAN:** on the Security tab, set the security parameters (e.g., WPA2-PSK and the PSK passkey).
5. **Verify and monitor:** check the WLANs list and the Monitor tab to confirm client association and operational status.

## 3. Configure a WPA2 Enterprise WLAN on the WLC

### External Management and Authentication Services
- **SNMP traps:** the WLC sends system log messages and traps to an external SNMP server for central monitoring.
  - Path: Management → SNMP → Trap Receivers → add the IP and community name.
- **RADIUS server (AAA):** required for WPA2 Enterprise. Users enter a username and password, which are checked against a central RADIUS database.
  - Path: Security → AAA → RADIUS → Authentication → add the IP, port (**1812**), and shared secret.

### Virtual VLAN Interfaces and DHCP Scopes
- **VLAN interface setup:** each WLAN needs its own virtual interface on the WLC.
  - Path: Controller → Interfaces → set the interface name, VLAN ID, physical port, IP address and subnet mask, gateway, and primary DHCP server IP.
- **Internal DHCP scope:**
  - Path: Controller → Internal DHCP Server → DHCP Scope → set the pool start and end range, network, subnet mask, default router, and status (Enabled).

### WPA2 Enterprise WLAN Implementation Steps
1. **Create a new WLAN:** set the profile name and SSID, and give it a unique WLAN ID.
2. **Enable and map the interface:** set the status to *Enabled* and map the WLAN to the VLAN interface.
3. **Check the encryption defaults:** under Security → Layer 2, make sure WPA2 Policy, AES encryption, and 802.1X key management are active.
4. **Assign the RADIUS server:** under Security → AAA Servers, select the configured RADIUS server address and port.
5. **Save and verify:** apply the settings and confirm the WLAN shows as enabled on the WLANs page.

## 4. Troubleshoot WLAN Issues

### Structured Troubleshooting Methodology
Follow the 6-step method:
1. **Identify the problem:** talk to the user and gather initial information.
2. **Establish a theory of probable causes:** brainstorm possible causes.
3. **Test the theory:** run quick tests or research to find the root cause.
4. **Establish a plan of action and implement it:** plan and carry out the fix.
5. **Verify full system functionality:** make sure everything works, and put preventive measures in place.
6. **Document findings, actions, and outcomes:** record the details for future reference.

### Troubleshooting Specific Issues

**Client cannot connect**
- Check the network settings with `ipconfig`.
- Confirm wired connectivity by pinging an IP address.
- Check or reload the wireless NIC drivers, or replace the NIC.
- Check that the security modes, passphrases, and channel settings match.
- Check the power, cables, and hardware status of the APs and switches.

**Poor wireless performance / slow speeds**
- **Coverage area:** make sure the host is inside the Basic Service Area (BSA) with no physical obstructions.
- **Interference:** check the 2.4 GHz band for overlapping channels or outside interference.
- **Upgrade legacy clients:** older 802.11b/g/n devices slow down the whole WLAN, so upgrade clients to a uniform, higher standard.
- **Split traffic:** separate 2.4 GHz traffic (basic browsing) from 5 GHz traffic (high bandwidth and media streaming) by creating separate SSIDs.

**Updating firmware**
- Keep AP and WLC software up to date to fix known bugs and patch security vulnerabilities.
- The WLC can pre-download firmware images and push them to all managed APs at once (Wireless → Access Points → Global Configuration).

## Exam Reminders
- First things to do on a SOHO router: change the default password and the default DHCP/IP settings.
- NAT lets many private hosts share one public IP by tracking source ports.
- Port forwarding opens fixed ports; port triggering opens them only after an outbound request.
- WLC trunk ports carry multiple WLANs/VLANs; each WLAN is mapped to a VLAN interface.
- Basic WLAN: create, enable, choose the interface, set security (PSK).
- WPA2 Enterprise: needs a RADIUS server (port 1812) and 802.1X key management.
- Troubleshooting: the same 6 steps as in CCNA 1 (identify, theory, test, plan, verify, document).
- Slow Wi-Fi: check coverage, channel interference, and legacy clients; use separate SSIDs for 2.4 and 5 GHz.
