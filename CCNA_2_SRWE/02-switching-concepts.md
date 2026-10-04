# Module 2: Switching Concepts

## 1. Frame Forwarding Concepts

### Key Terms
- **Ingress:** a frame entering an interface.
- **Egress:** a frame exiting an interface.

### Switch Forwarding Decisions
- Switches forward frames based on the **ingress interface** and the **destination MAC address**.
- The switch uses its **MAC address table** to make the forwarding decision.
- **Rule:** a switch never forwards a frame out the same interface it was received on.

## 2. The Switch MAC Address Table

### CAM Table
- The MAC address table is also called a **Content Addressable Memory (CAM)** table.
- It is used to find the **egress interface** from the destination MAC address.

### Learn and Forward Process

**Step 1: Learn (examines the source address)**
- If the source MAC address isn't in the table, the switch adds it with the port number.
- If the source MAC address is already in the table, the timeout is reset to **5 minutes**.

**Step 2: Forward (examines the destination address)**

| Destination | Action |
|-------------|--------|
| **Known** (MAC is in the table) | Forward the frame out the specified port |
| **Unknown** (MAC is not in the table) | Flood the frame out all interfaces except the ingress port |

## 3. Switch Forwarding Methods
Switches use software on **ASICs** (Application-Specific Integrated Circuits) to make fast decisions.

### Store-and-Forward Switching
- Receives the **entire frame** and makes sure it is valid before forwarding.
- Cisco's **preferred** switching method.
- **Error checking:** checks the Frame Check Sequence (FCS) for Cyclic Redundancy Check (CRC) errors and discards bad frames.
- **Buffering:** the ingress interface buffers the frame while the FCS is checked, which also allows for differences in port speeds.

### Cut-Through Switching
- Forwards the frame as soon as the destination MAC address and egress port are determined.
- Suited to environments that need latency under **10 microseconds**.
- Does **not** check the FCS, so it can pass along errors and waste bandwidth.
- Can't support links with different speeds on the ingress and egress ports.
- **Fragment-free:** a cut-through variation that checks the destination and makes sure the frame is at least **64 bytes** long, to remove runts before forwarding.

### Store-and-Forward vs. Cut-Through

| | Store-and-forward | Cut-through |
|--|-------------------|-------------|
| Waits for | The entire frame | The destination MAC address |
| Checks FCS/CRC | Yes, discards bad frames | No |
| Latency | Higher | Very low (under 10 microseconds) |
| Different port speeds | Supported (buffering) | Not supported |

## 4. Switching Domains

### Collision Domains
- Switches remove collision domains when operating in **full-duplex**.
- If one or more devices run in **half-duplex**, a collision domain is created because of bandwidth contention.
- Most devices (Cisco, Microsoft) use **auto-negotiation** for speed and duplex by default.

### Broadcast Domains
- Extends across all Layer 1 or Layer 2 devices on a LAN.
- Made up of all the devices on the LAN that receive broadcast traffic.
- Only a **Layer 3 device (a router)** breaks up a broadcast domain.
- Adding Layer 1 or Layer 2 devices makes the broadcast domain bigger.
- Too many broadcasts cause congestion and slow the network down.

## 5. Network Congestion Mitigation
Switches reduce network congestion with these features:
- **Fast port speeds:** ports support high speeds (up to 100 Gbps depending on the model).
- **Fast internal switching:** fast internal buses or shared memory boost performance.
- **Large frame buffers:** temporary storage for processing large volumes of frames.
- **High port density:** many ports for direct LAN connections at lower cost, so more local traffic can be handled with less congestion.

## Exam Reminders
- Ingress = in, egress = out. A switch never sends a frame back out the port it came in on.
- Learn from the **source** MAC; forward by the **destination** MAC.
- MAC table timeout = 5 minutes. Unknown destination = flood.
- Store-and-forward checks FCS and is Cisco's preferred method; cut-through doesn't check FCS.
- Fragment-free checks the first 64 bytes.
- Full-duplex switch ports = no collision domain. Only a router breaks up a broadcast domain.
