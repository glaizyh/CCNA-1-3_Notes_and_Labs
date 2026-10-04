# Module 12: WLAN Concepts

## 1. Introduction to Wireless

### Benefits and Types of Wireless Networks
- **WLAN benefits:** mobility in homes, offices, and campuses, and easy adaptation to changing technologies and network needs.

| Type | Description |
|------|-------------|
| **WPAN** (Wireless Personal-Area Network) | Low power, short range (20-30 ft or 6-9 m), operating at 2.4 GHz, based on IEEE 802.15 (e.g., Bluetooth, Zigbee) |
| **WLAN** (Wireless Local Area Network) | Medium-sized network, up to about 300 ft, at 2.4 GHz or 5.0 GHz, based on IEEE 802.11 |
| **WMAN** (Wireless Metropolitan Area Network) | Covers large geographic areas such as cities or districts, using specific licensed frequencies |
| **WWAN** (Wireless Wide Area Network) | Covers wide national or global areas, using licensed cellular frequencies |

### Wireless Technologies
- **Bluetooth:** IEEE WPAN standard for pairing devices up to 300 ft (100 m).
  - *BLE (Bluetooth Low Energy):* supports mesh topology for large numbers of devices.
  - *BR/EDR (Basic Rate/Enhanced Data Rate):* point-to-point topologies, optimized for audio streaming.
- **WiMAX:** IEEE 802.16 standard for broadband wireless access up to 30 miles (50 km).
- **Cellular broadband:** carries voice and data over GSM (used internationally) or CDMA (mainly the US) networks.
- **Satellite broadband:** needs a directional satellite dish aligned with a geostationary orbit and a clear line of sight. Used in rural locations.

### 802.11 Standards and Frequencies
- **2.4 GHz (UHF):** 802.11, 802.11b, 802.11g, 802.11n, 802.11ax.
- **5 GHz (SHF):** 802.11a, 802.11n, 802.11ac, 802.11ax.

| Standard | Frequency | Max data rate / key features |
|----------|-----------|------------------------------|
| **802.11** | 2.4 GHz | Up to 2 Mb/s |
| **802.11a** | 5 GHz | Up to 54 Mb/s; not interoperable with 802.11b/g |
| **802.11b** | 2.4 GHz | Up to 11 Mb/s; longer range and better penetration of structures |
| **802.11g** | 2.4 GHz | Up to 54 Mb/s; backward compatible with 802.11b |
| **802.11n** | 2.4 / 5 GHz | 150-600 Mb/s; uses MIMO |
| **802.11ac** | 5 GHz | 450 Mb/s to 1.3 Gb/s; supports up to 8 antennas |
| **802.11ax** | 2.4 / 5 GHz | High-Efficiency Wireless (HEW); can also use 1 GHz and 7 GHz |

### Standards Organizations
- **ITU (International Telecommunication Union):** regulates radio spectrum allocation and satellite orbits internationally.
- **IEEE:** maintains the standards for local and metropolitan area networks (the IEEE 802 LAN/MAN family) and radio frequency modulation.
- **Wi-Fi Alliance:** an association of vendors that promotes WLAN growth and tests product interoperability based on 802.11 standards.

## 2. WLAN Components

### Network Adapters and Access Devices
- **Wireless NICs:** radio transmitters/receivers built into client devices. A USB wireless adapter can be added if a device lacks one.
- **Wireless home router:** a multifunction device that works as an **access point** (wireless access), a **switch** (connecting wired devices), and a **router** (default gateway).
- **Wireless access point (AP):** clients associate and authenticate with an AP using their wireless NIC to reach network resources.

### AP Categories

| Category | Description |
|----------|-------------|
| **Autonomous APs** | Standalone devices configured and managed manually with the CLI or GUI. Each AP acts on its own |
| **Controller-based APs** | Lightweight APs (LAPs) managed centrally by a Wireless LAN Controller (WLC) using LWAPP or CAPWAP. The WLC configures them automatically |

### Antenna Types
- **Omnidirectional:** 360-degree coverage; ideal for homes and standard offices.
- **Directional:** focuses the signal in one direction (e.g., Yagi and parabolic dish).
- **MIMO (Multiple Input Multiple Output):** uses multiple antennas (up to 8) to raise total bandwidth.

## 3. WLAN Operation

### Topology Modes
- **Ad hoc mode:** connects clients peer to peer without an AP.
- **Infrastructure mode:** connects clients through an AP.
- **Tethering:** an ad hoc variation where a smartphone or tablet makes a personal hotspot over cellular data.

### Service Sets
- **BSS (Basic Service Set):** one AP connecting clients within a Basic Service Area (BSA). Identified by the **BSSID** (the AP's MAC address).
- **ESS (Extended Service Set):** two or more BSSs joined by a wired distribution system. Lets clients roam across the Extended Service Area (ESA).

### CSMA/CA Operation
WLANs run in half-duplex, so collision detection isn't possible. They use **CSMA/CA** to control access:
1. Listen to check that the channel is idle.
2. Send a **Ready to Send (RTS)** message to the AP.
3. Receive a **Clear to Send (CTS)** message from the AP, granting access.
4. Transmit data.
5. Wait for an **acknowledgment (ACK)**. If none arrives, assume a collision happened and start over.

### AP Association
**Required parameters:** SSID, password, network mode (802.11 standard), security mode (WEP, WPA, WPA2, WPA3), and channel settings.

| Scanning mode | How it works |
|---------------|--------------|
| **Passive** | The AP periodically broadcasts **Beacon** frames with its SSID, supported standards, and security settings |
| **Active** | The client broadcasts **Probe Request** frames on several channels, and APs answer with a **Probe Response** |

## 4. CAPWAP Operation

### Protocol Fundamentals
- **CAPWAP:** a standard protocol that lets a WLC manage multiple LAPs and WLANs over IPv4 or IPv6. It is an **IETF** standard (RFC 5415).
- **Ports:** UDP **5246** (control) and **5247** (data).
- **Security:** derived from LWAPP, with **DTLS** (Datagram Transport Layer Security) encryption added.

### Split MAC Architecture
MAC functions are divided between the AP and the WLC:

| AP MAC functions | WLC MAC functions |
|------------------|-------------------|
| Beacons and probe responses | Authentication |
| Packet acknowledgments and retransmissions | Association and re-association of roaming clients |
| Frame queuing and packet prioritization | Frame translation to other protocols |
| MAC layer data encryption and decryption | Termination of 802.11 traffic on a wired interface |

### DTLS Encryption
- **Control channel:** DTLS is **enabled by default**, to secure management and control traffic.
- **Data channel:** DTLS is **disabled by default** and needs a DTLS license installed on the WLC.

### FlexConnect Modes
FlexConnect lets you configure and control APs over WAN links:
- **Connected mode:** the WLC is reachable over the CAPWAP tunnel and does all the functions.
- **Standalone mode:** the WLC is unreachable, so the FlexConnect AP switches client traffic and authenticates clients locally.

## 5. Channel Management

### Spread Spectrum and Modulation Techniques

| Technique | Description |
|-----------|-------------|
| **DSSS** (Direct-Sequence Spread Spectrum) | Spreads the signal over a wider frequency band; used by 802.11b devices to avoid interference |
| **FHSS** (Frequency-Hopping Spread Spectrum) | Transmits by rapidly switching carrier frequencies; used by legacy 802.11 |
| **OFDM** (Orthogonal Frequency-Division Multiplexing) | Divides one channel into multiple sub-channels on adjacent frequencies; used by 802.11a/g/n/ac |

### Channel Selection Guidelines
- **2.4 GHz:** channels are 22 MHz wide, separated by 5 MHz. The non-overlapping channels are **1, 6, and 11**.
- **5 GHz:** 24 non-overlapping channels, separated by 20 MHz. Typical non-overlapping channels include **36, 48, and 60**.
- **Deployment planning:** AP placement depends on facility layout, user density, target data rates, channel overlap, and AP transmit power.

## 6. WLAN Threats

| Attack | Description and mitigation |
|--------|----------------------------|
| **Interception of data** | Unencrypted wireless traffic can be intercepted by anyone listening within range |
| **Denial of Service (DoS)** | Caused by misconfigured devices, deliberate interference, or rogue RF signals |
| **Rogue APs** | Unauthorized APs or hotspots connected to a corporate network, letting attackers sniff data or launch attacks. Mitigated with WLC policies and active radio spectrum monitoring |
| **Man-in-the-middle (evil twin)** | An attacker sets up a rogue AP with the **same SSID** as a legitimate network. Mitigated with strong mutual authentication |

## 7. Secure WLANs

### Legacy Security Features
- **SSID cloaking:** turns off the SSID broadcast in beacon frames, so clients must type the SSID. *Not a complete security measure.*
- **MAC address filtering:** allows or denies access based on the MAC address.

### Authentication Systems
- **Open system authentication:** no password needed; common in public spaces. Clients must rely on end-to-end security (e.g., VPNs).
- **Shared key authentication:** needs a pre-shared password to authenticate and encrypt traffic.

### Shared Key Authentication Frameworks

| Method | Encryption | Notes |
|--------|------------|-------|
| **WEP** | RC4 | Static key; weak and insecure; **never use** |
| **WPA** | TKIP | Changes WEP by using a different key per packet (TKIP) |
| **WPA2** | AES (CCMP) | Uses the Advanced Encryption Standard; the current robust standard |
| **WPA3** | AES / SAE | Next-generation standard; disallows legacy protocols; requires Protected Management Frames (PMF) |

### Personal vs. Enterprise Modes
- **Personal** (WPA/WPA2/WPA3-Personal): uses a Pre-Shared Key (PSK) password; no authentication server is needed.
- **Enterprise:** needs an **AAA RADIUS server**, using **IEEE 802.1X** and **EAP** to authenticate users.
  - RADIUS needs: the server's IP address, the UDP ports (**1812** authentication, **1813** accounting), and a shared secret key.

### WPA3 Enhancements
- **WPA3-Personal:** uses **Simultaneous Authentication of Equals (SAE)** to protect against brute-force password guessing.
- **WPA3-Enterprise:** uses 802.1X/EAP with a **192-bit** cryptographic suite.
- **Open networks:** use **Opportunistic Wireless Encryption (OWE)** to encrypt unauthenticated traffic automatically.
- **IoT onboarding:** uses **Device Provisioning Protocol (DPP)** to connect headless IoT devices securely.

## Exam Reminders
- Non-overlapping 2.4 GHz channels: **1, 6, 11**. 802.11b/g/n use 2.4 GHz; 802.11a/ac use 5 GHz.
- WLANs use **CSMA/CA** (half-duplex): RTS, CTS, data, ACK.
- BSSID = AP MAC address; ESS = two or more BSSs.
- Autonomous APs are configured one by one; controller-based (lightweight) APs are managed by a WLC with CAPWAP (UDP 5246 and 5247).
- Passive scanning = beacons; active scanning = probe request and response.
- Security order, weakest to strongest: WEP, WPA (TKIP), WPA2 (AES), WPA3 (SAE).
- Enterprise mode = RADIUS (UDP 1812 and 1813) with 802.1X and EAP.
- Evil twin = rogue AP with the same SSID.
