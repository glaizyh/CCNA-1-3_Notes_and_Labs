# Module 4: Physical Layer

## 1. Purpose of the Physical Layer

### The Physical Connection
- A physical connection to a local network must exist before any network communication can happen. It can be wired or wireless, in a corporate office or a home.
- A **NIC (Network Interface Card)** connects a device to the network. Some devices have one NIC; others have several (wired and/or wireless).
- Not all physical connections offer the same level of performance.

### The Physical Layer Function
- Transports **bits** across the network media.
- Accepts a complete frame from the Data Link layer and encodes it as a series of signals sent onto the local media.
- This is the **last step in encapsulation**.
- The next device in the path receives the bits, re-encapsulates the frame, and decides what to do with it.

## 2. Physical Layer Characteristics

### Physical Layer Standards
- **TCP/IP standards:** implemented in **software**, governed by the IETF.
- **Physical layer standards:** implemented in **hardware**, governed by ISO, EIA/TIA, ITU-T, ANSI, and IEEE.

### Functional Areas
Physical layer standards cover three areas:

| Area | Description |
|------|-------------|
| **Physical components** | Hardware devices, media, and connectors that carry the signals representing bits (NICs, interfaces, connectors, cable materials, cable designs) |
| **Encoding** | Converts the bit stream into a format the next device recognizes, using predictable patterns. Examples: Manchester, 4B/5B, 8B/10B |
| **Signaling** | How the bit values "1" and "0" are represented on the medium; varies by media type |

**Signaling by media type**

| Media | Signal |
|-------|--------|
| Fiber-optic | Light pulses |
| Copper | Electrical signals (voltage) |
| Wireless | Microwave signals (AM, FM, PM) |

### Bandwidth
- **Bandwidth** is the capacity at which a medium can carry data.
- **Digital bandwidth** is the amount of data that can flow from one place to another in a given time (bits per second).
- Media properties, current technologies, and the laws of physics all affect available bandwidth.

| Unit | Abbreviation | Equivalence |
|------|--------------|-------------|
| Bits per second | bps | Fundamental unit of bandwidth |
| Kilobits per second | Kbps | 1 Kbps = 1,000 bps = 10³ bps |
| Megabits per second | Mbps | 1 Mbps = 1,000,000 bps = 10⁶ bps |
| Gigabits per second | Gbps | 1 Gbps = 1,000,000,000 bps = 10⁹ bps |
| Terabits per second | Tbps | 1 Tbps = 1,000,000,000,000 bps = 10¹² bps |

### Bandwidth Terminology
- **Latency:** time, including delays, for data to travel from one point to another.
- **Throughput:** measure of the transfer of bits across the media over a given period.
- **Goodput:** measure of **usable** data transferred over a given period.

```
Goodput = Throughput - Traffic overhead
```

## 3. Copper Cabling

### Characteristics
- The most common cabling in networks today: inexpensive, easy to install, and low resistance to electrical current.

**Limitations**
- **Attenuation:** the longer the signal travels, the weaker it gets.
- **Interference:** susceptible to EMI (electromagnetic interference), RFI (radio frequency interference), and **crosstalk**, which can distort and corrupt signals.

**Mitigation**
- Follow cable length limits to reduce attenuation.
- Metallic shielding and grounding reduce EMI/RFI.
- Twisting opposing circuit pair wires together reduces crosstalk.

### Types of Copper Cabling

**1. Unshielded Twisted-Pair (UTP)**
- Most common networking media.
- Terminated with RJ-45 connectors.
- Interconnects hosts with intermediary network devices.
- Components: (1) outer jacket protects against physical damage, (2) twisted pairs protect the signal from interference, (3) color-coded plastic insulation isolates the wires and identifies each pair.

**2. Shielded Twisted-Pair (STP)**
- Better noise protection than UTP, but more expensive and harder to install.
- Terminated with RJ-45 connectors.
- Interconnects hosts with intermediary network devices.
- Components: (1) outer jacket, (2) braided or foil shield for EMI/RFI protection, (3) foil shield around each pair of wires for EMI/RFI protection, (4) color-coded plastic insulation.

**3. Coaxial cable**
- Components: (1) outer jacket prevents minor physical damage, (2) woven copper braid or metallic foil acts as the second wire in the circuit and shields the inner conductor, (3) flexible plastic insulation, (4) copper conductor that carries the signal.
- Connectors: BNC, N type, F type.
- Common uses: wireless installations (attaching antennas) and cable internet (customer premises wiring).

## 4. UTP Cabling

### Properties
UTP has four pairs of color-coded copper wires twisted together in a flexible plastic sheath, with no shielding. It limits crosstalk through:
- **Cancellation:** each wire in a pair has opposite polarity (one negative, one positive). When twisted, their magnetic fields cancel each other and outside EMI/RFI.
- **Variation in twists per foot:** each pair is twisted a different amount, which helps prevent crosstalk between pairs.

### Standards and Connectors
- **TIA/EIA-568** sets cable types, lengths, connectors, termination, and testing methods.
- **IEEE** sets electrical standards and rates cable by performance (Category 3, Category 5/5e, Category 6).
- Connectors: RJ-45 plug and RJ-45 socket.

### Wiring Standards and Cable Types

| Cable type | Wiring standard | Application |
|------------|-----------------|-------------|
| Ethernet straight-through | Both ends T568A or both ends T568B | Host to network device |
| Ethernet crossover | One end T568A, other end T568B | Host-to-host, switch-to-switch, router-to-router (considered legacy because of Auto-MDIX) |
| Rollover | Cisco proprietary | Host serial port to router or switch console port (using an adapter) |

## 5. Fiber-Optic Cabling

### Properties
- Less common than UTP because of cost, but ideal for some scenarios.
- Carries data over longer distances and at higher bandwidth than any other media.
- Less susceptible to attenuation and **completely immune to EMI/RFI**.
- Made of flexible, extremely thin strands of very pure glass.
- Uses a laser or LED to encode bits as pulses of light.
- Acts as a wave guide, moving light between two ends with minimal signal loss.

### Types of Fiber Media

| Feature | Single-Mode Fiber (SMF) | Multimode Fiber (MMF) |
|---------|-------------------------|-----------------------|
| Light path | A single straight path for light | Multiple paths for light |
| Core size | Very small (9 micron glass core) | Larger (50 or 62.5 micron glass core) |
| Light source | Expensive lasers | Less expensive LEDs, transmitting at different angles |
| Distance and rate | Long-distance applications | Up to 10 Gbps over 550 meters |
| Dispersion | Less dispersion | Greater dispersion (limits maximum distance to 550 m) |

*Dispersion is the spreading out of a light pulse over time. More dispersion means more loss of signal strength.*

### Industry Usage
1. **Enterprise networks:** backbone cabling and interconnecting infrastructure devices.
2. **Fiber-to-the-Home (FTTH):** always-on broadband for homes and small businesses.
3. **Long-haul networks:** service providers connecting countries and cities.
4. **Submarine cable networks:** high-speed, high-capacity links that survive harsh undersea environments over transoceanic distances.

### Connectors and Patch Cords
- **Connectors:** Straight-Tip (ST), Subscriber Connector (SC), Lucent Connector (LC) simplex, duplex multimode LC.
- **Jacket colors:** yellow for single-mode; orange or aqua for multimode.
- **Patch cords:** SC-SC MM, LC-LC SM, ST-LC MM, ST-SC SM.

### Fiber vs. Copper

| Issue | UTP | Fiber-optic |
|-------|-----|-------------|
| Bandwidth supported | 10 Mb/s to 10 Gb/s | 10 Mb/s to 100 Gb/s |
| Distance | Relatively short (1 - 100 m) | Relatively long (1 - 100,000 m) |
| Immunity to EMI/RFI | Low | High (completely immune) |
| Immunity to electrical hazards | Low | High (completely immune) |
| Media and connector costs | Lowest | Highest |
| Installation skills required | Lowest | Highest |
| Safety precautions | Lowest | Highest |

## 6. Wireless Media

### Properties and Limitations
- Carries electromagnetic signals representing binary digits using radio or microwave frequencies.
- Gives the greatest mobility.

**Limitations**
- **Coverage area:** effective coverage can be greatly affected by the physical characteristics of the location.
- **Interference:** susceptible to interference, and can be disrupted by common devices.
- **Security:** no physical media strand is needed, so anyone in range can access the transmission.
- **Shared medium:** WLANs operate in **half-duplex** (only one device can send or receive at a time). Many users at once means less bandwidth per user.

### Wireless Standards
IEEE and telecommunications industry standards specify data-to-radio signal encoding, transmission frequency and power, signal reception and decoding, and antenna design.

| Technology | Standard | Description |
|------------|----------|-------------|
| Wi-Fi | IEEE 802.11 | Wireless LAN (WLAN) technology |
| Bluetooth | IEEE 802.15 | Wireless Personal Area Network (WPAN) standard |
| WiMAX | IEEE 802.16 | Point-to-multipoint topology for broadband wireless access |
| Zigbee | IEEE 802.15.4 | Low data-rate, low power communications, mainly for IoT |

### Wireless LAN Components
- **Wireless Access Point (AP):** concentrates wireless signals from users and connects to the existing copper-based network infrastructure.
- **Wireless NIC adapters:** give network hosts wireless communication capability.
