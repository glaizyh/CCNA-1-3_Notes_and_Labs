# Module 9: QoS Concepts

## 1. Network Transmission Quality

### Bandwidth, Congestion, Delay, and Jitter

- **Bandwidth:** Measured in bits per second (bps); defines the capacity of a link.
- **Congestion:** Occurs when an interface receives more traffic than it can handle. Common congestion points:
  - Aggregation
  - Speed mismatch
  - LAN-to-WAN transitions
- **Delay (Latency):** Time taken for a packet to travel from source to destination.
  - *Fixed delay:* Specific time required for fixed processes (e.g., placing bits on the media).
  - *Variable delay:* Unspecified time affected by traffic volumes and queuing.
- **Jitter:** The variation in the arrival delay of packets.

### Types of Delay

| Delay Type | Fixed / Variable | Description |
| --- | --- | --- |
| **Code Delay** | Fixed | Time to compress data at the source before transmission |
| **Packetization Delay** | Fixed | Time to encapsulate a packet with header information |
| **Queuing Delay** | Variable | Time a packet waits in a buffer to be transmitted |
| **Serialization Delay** | Fixed | Time to transmit a frame onto the wire bit by bit |
| **Propagation Delay** | Variable | Time for the frame to traverse the physical distance |
| **De-jitter Delay** | Fixed | Time to buffer packets and play them out in evenly spaced intervals |

### Packet Loss & Playout Buffer

- **Queuing behavior:** Devices hold excess packets in memory until resources are free. If memory fills completely, **packet loss (drops)** occurs.
- **Playout delay buffer:** Buffers Real-Time Protocol (RTP) digital audio streams to smooth out jitter.
- **Excessive jitter:** If jitter exceeds buffer capabilities, packets are discarded, causing audible audio dropouts.
  - Digital Signal Processors (DSPs) can interpolate a single lost packet.
  - Severe loss degrades audio quality.

---

## 2. Traffic Characteristics

### Traffic Requirements Matrix

| Traffic Type | Bandwidth | Latency | Jitter | Packet Loss | Characteristics |
| --- | --- | --- | --- | --- | --- |
| **Voice** | 30–128 Kbps | ≤ 150 ms | ≤ 30 ms | ≤ 1% | Smooth, benign, drop-sensitive, delay-sensitive |
| **Video** | 384 Kbps – 20 Mbps | 200–400 ms | 30–50 ms | 0.1–1% | Bursty, greedy, drop-sensitive, delay-sensitive |
| **Data** | Variable | Insensitive | Insensitive | Insensitive (TCP retransmits) | Smooth or bursty, benign or greedy, uses TCP |

### Traffic Specifics

- **Voice protocols:** Uses RTP ports 16384–32767.
- **Video protocols:** Uses RTSP (UDP port 554). Packet count and size vary every 33 ms.
- **Data prioritization (QoE):**
  - Interactive / mission-critical data should be prioritized for a 1–2 second response time.
  - Non-mission-critical data receives leftover bandwidth.

---

## 3. Queuing Algorithms

### First-In, First-Out (FIFO)

- Buffers and forwards packets strictly in order of arrival.
- No concept of priority, traffic classes, or packet differentiation.
- Represents the complete absence of QoS.

### Weighted Fair Queuing (WFQ)

- Automated scheduling method that allocates bandwidth dynamically based on weights assigned to identified traffic flows.
- Classifies traffic using IP/MAC addresses, ports, protocol, and ToS values.
- **Limitation:** Does **not** work with tunneling or encryption because packet payload headers are hidden.

### Class-Based Weighted Fair Queuing (CBWFQ)

- Extends WFQ by supporting user-defined traffic classes using match criteria (ACLs, protocols, input interfaces).
- Assigns a dedicated FIFO queue with guaranteed bandwidth and queue limits to each class.
- **Tail drop:** Occurs when a class queue limit is reached; all subsequent arriving packets are dropped.

### Low Latency Queuing (LLQ)

- Adds **Strict Priority Queuing (PQ)** to CBWFQ.
- Allows delay-sensitive traffic (like voice) to be serviced first, before any other queues are checked.
- **Best practice:** Direct **only** voice traffic to the strict priority queue to avoid starving other queues.

---

## 4. QoS Models

| Model | Scalability | Guarantee Level | Resource Intensity | Key Features |
| --- | --- | --- | --- | --- |
| **Best-Effort** | Highly scalable | None (no priority) | None | Default internet delivery; treats all packets equally |
| **IntServ** | Very low (microflows) | Absolute guarantee | High (stateful signaling) | Connection-oriented; uses **RSVP** for end-to-end resource reservation |
| **DiffServ** | Highly scalable | Relative / per-hop | Low (stateless aggregates) | Classifies flows into aggregates and applies hop-by-hop QoS policies |

---

## 5. QoS Implementation Techniques

### Categories of QoS Tools

1. **Classification and Marking:** Analyzes flows to identify classes and marks headers on ingress.
2. **Congestion Avoidance:** Monitors queue depth to prevent buffer saturation (e.g., WRED).
3. **Congestion Management:** Manages output queues during congestion via queuing and scheduling algorithms (CBWFQ, LLQ).

### Layer 2 & Layer 3 Marking Fields

| Layer | Technology / Standard | Marking Field | Bit Width |
| --- | --- | --- | --- |
| Layer 2 | Ethernet (802.1Q / 802.1p) | Class of Service (CoS) / Priority (PRI) | 3 bits (0–7) |
| Layer 2 | 802.11 (Wi-Fi) | Wi-Fi Traffic Identifier (TID) | 3 bits |
| Layer 2 | MPLS | Experimental (EXP) | 3 bits |
| Layer 3 | IPv4 / IPv6 (legacy, RFC 791) | IP Precedence (IPP) | 3 bits |
| Layer 3 | IPv4 / IPv6 (modern, RFC 2474) | Differentiated Services Code Point (DSCP) | 6 bits (64 values) |

### Layer 2 CoS Values (802.1p)

| CoS | Meaning |
| --- | --- |
| `0` | Best-effort data |
| `1` | Medium-priority data |
| `2` | High-priority data |
| `3` | Call signaling |
| `4` | Videoconferencing |
| `5` | Voice bearer (voice traffic) |
| `6`, `7` | Reserved |

### Layer 3 Marking: ToS and Traffic Class Fields

- **Field names:** IPv4 **Type of Service (ToS)** and IPv6 **Traffic Class** (8 bits total).
- **DSCP structure:** First 6 bits are DSCP (64 possible values); remaining 2 bits are Explicit Congestion Notification (ECN).

**DSCP categories**

- **Best-Effort (BE):** Default value `0`.
- **Expedited Forwarding (EF):** Decimal `46` (binary `101110`); mapped to CoS 5 for voice traffic.
- **Assured Forwarding (AF):** Uses the format `AFxy`:
  - `x` = Class (1–4; **4 is best**)
  - `y` = Drop preference (1–3; **3 is highest drop**)
  - *Example:* `AF32` = Class 3, medium drop → binary `011100` → decimal `28`
- **Class Selector (CS):** Uses the first 3 bits to maintain backward compatibility with 3-bit IP Precedence and CoS values.

### Trust Boundaries

- Classify and mark traffic as close to its source as technically and administratively feasible.
- **Trusted endpoints:** IP phones, servers, and management systems capable of marking traffic directly.
- **Non-trusted boundaries:** Re-mark incoming traffic from untrusted hosts to CoS/DSCP `0` at the access switch layer.

### Congestion Avoidance: WRED

- **Weighted Random Early Detection (WRED):** Randomly drops lower-priority packets before queues fill completely.
- Causes TCP senders to gracefully throttle back their window sizes, avoiding global TCP synchronization and tail drops.

### Traffic Shaping vs. Traffic Policing

| | Traffic Shaping | Traffic Policing |
| --- | --- | --- |
| **Action on excess** | Buffers excess packets and schedules them for smooth transmission over time | Drops or re-marks excess traffic immediately |
| **Trigger** | Configured rate | Configured rate limit (Committed Information Rate, CIR) exceeded |
| **Applied on** | Outbound (egress) interfaces | Inbound (ingress) interfaces |
