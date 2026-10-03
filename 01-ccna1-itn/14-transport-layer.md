# Module 14: Transport Layer

## 1. Transportation of Data

### Role of the Transport Layer
- Responsible for **logical communication between applications** running on different hosts.
- Acts as the link between the application layer and the lower layers that handle network transmission.
- Moves data between applications on devices in the network.

### Transport Layer Responsibilities
- **Tracking individual conversations:** keeps track of each conversation between applications on the source and destination hosts.
- **Segmenting data and reassembling segments:** divides application data into blocks called **segments**, adds header information to each, and reassembles the data at the destination.
- **Identifying applications:** uses **port numbers** to identify, separate, and manage multiple conversations at the same time.
- **Multiplexing:** segmentation and multiplexing let different conversations be interleaved on the same network at the same time.

### Transport Layer Protocols
- IP doesn't specify how packets are delivered or transported.
- Transport layer protocols specify how to transfer messages between hosts and manage the reliability requirements of a conversation.
- The two main protocols are **TCP** (Transmission Control Protocol) and **UDP** (User Datagram Protocol).

### Protocol Selection Criteria

| | TCP | UDP |
|--|-----|-----|
| **Properties** | Reliable, acknowledges data, resends lost data, delivers data in order | Fast, low overhead, no acknowledgments, doesn't resend lost data, delivers data as it arrives |
| **Used for** | HTTP/HTTPS (web), SMTP/IMAP (email), FTP (file transfer) | VoIP (IP telephony), DNS, TFTP |

## 2. TCP Overview

### TCP Features
- **Establishes a session:** TCP is connection-oriented. It negotiates and sets up a connection (session) between source and destination before any traffic is forwarded.
- **Ensures reliable delivery:** guarantees each segment arrives by retransmitting lost or corrupted data.
- **Same-order delivery:** reorders segments using sequence numbers, whatever path they took.
- **Flow control:** asks the sending application to slow down when hosts or resources are overloaded.
- **Stateful protocol:** keeps track of the state of the session by recording what has been sent and acknowledged.

### TCP Header Structure
The TCP header is **20 bytes**.

| # | Field | Size | Description |
|---|-------|------|-------------|
| 1 | Source port | 16 bits | Identifies the originating application by port number |
| 2 | Destination port | 16 bits | Identifies the receiving application by port number |
| 3 | Sequence number | 32 bits | Used for data reassembly |
| 4 | Acknowledgment number | 32 bits | Shows data received and the next byte expected from the source |
| 5 | Header length (data offset) | 4 bits | Length of the TCP segment header |
| 6 | Reserved | 6 bits | Reserved for future use |
| 7 | Control bits | 6 bits | Flags showing the purpose and function of the segment |
| 8 | Window size | 16 bits | Number of bytes that can be accepted at one time |
| 9 | Checksum | 16 bits | Error checking of the header and data |
| 10 | Urgent | 16 bits | Shows whether the data is urgent |

**Applications that use TCP:** HTTP, FTP, SMTP, SSH.

## 3. UDP Overview

### UDP Features
- Connectionless, with very little overhead and minimal data checking.
- Best-effort delivery, with no acknowledgment of receipt.
- Rebuilds data in the exact order it arrives.
- Lost datagrams are **not** resent.
- No session establishment.
- The sender isn't told whether the receiver has resources available.

### UDP Header Structure
The UDP header is **8 bytes (64 bits)** with only four fields:

| # | Field | Size | Description |
|---|-------|------|-------------|
| 1 | Source port | 16 bits | Identifies the source application |
| 2 | Destination port | 16 bits | Identifies the destination application |
| 3 | Length | 16 bits | Length of the UDP datagram (header plus data) |
| 4 | Checksum | 16 bits | Error checking of the header and data |

### Applications That Use UDP
1. **Live video and multimedia:** low delay tolerance (VoIP, video conferencing).
2. **Simple request/reply applications:** small transactions (DNS, DHCP).
3. **Self-reliant applications:** handle flow control and error recovery themselves (SNMP, TFTP).

## 4. Port Numbers

### Conversation Management
- **Port numbers** let TCP and UDP manage many conversations at the same time.
- **Source port:** generated dynamically by the originating device to identify the local conversation.
- **Destination port:** tied to the service or application on the remote host.
- **Socket:** an IP address combined with a port number (e.g., `192.168.1.5:1305`). Sockets let multiple processes on a host tell each other apart.

### Port Number Groups

| Group | Range | Description |
|-------|-------|-------------|
| **Well-known ports** | 0 to 1,023 | Reserved for common services and applications (web browsers, email clients) |
| **Registered ports** | 1,024 to 49,151 | Assigned by IANA to requesting entities for specific user applications |
| **Private / dynamic ports** | 49,152 to 65,535 | Ephemeral ports assigned dynamically by the client OS when it starts a connection |

### Well-Known Port Numbers

| Port | Protocol | Application |
|------|----------|-------------|
| 20 / 21 | TCP | File Transfer Protocol (FTP): data / control |
| 22 | TCP | Secure Shell (SSH) |
| 23 | TCP | Telnet |
| 25 | TCP | Simple Mail Transfer Protocol (SMTP) |
| 53 | UDP / TCP | Domain Name System (DNS) |
| 67 / 68 | UDP | DHCP server / client |
| 69 | UDP | Trivial File Transfer Protocol (TFTP) |
| 80 | TCP | Hypertext Transfer Protocol (HTTP) |
| 110 | TCP | Post Office Protocol version 3 (POP3) |
| 143 | TCP | Internet Message Access Protocol (IMAP) |
| 161 | UDP | Simple Network Management Protocol (SNMP) |
| 443 | TCP | Hypertext Transfer Protocol Secure (HTTPS) |

### Verification Tool
`netstat` shows the active TCP connections on a host.

## 5. TCP Communication Process

### TCP Three-Way Handshake
1. **SYN:** the client sends a segment with the SYN flag set to request a session with the server.
2. **SYN-ACK:** the server acknowledges the client and sends a segment with SYN and ACK set to request the server-to-client session.
3. **ACK:** the client replies with ACK set to establish the connection.

What the handshake does:
- Verifies that the destination device is on the network.
- Confirms an active service is listening on the intended port.
- Tells the destination the client wants to start a session.

### TCP Session Termination (Four-Way Handshake)
1. **FIN:** the sender sends FIN when it has no more data to send.
2. **ACK:** the receiver acknowledges the FIN.
3. **FIN:** the receiver sends its own FIN to end its side of the session.
4. **ACK:** the original sender replies with an ACK to finish the termination.

### TCP Control Bit Flags

| Flag | Meaning |
|------|---------|
| **URG** | Urgent pointer field is significant |
| **ACK** | Acknowledgment; used in connection setup and termination |
| **PSH** | Push function |
| **RST** | Resets the connection when an error or timeout occurs |
| **SYN** | Synchronizes sequence numbers during connection setup |
| **FIN** | No more data from the sender; used in termination |

### Reliability and Flow Control

**Guaranteed and ordered delivery**
- TCP uses sequence numbers in the headers to put segments back in order if they arrive out of order.

**Data loss and retransmission**
- Unacknowledged data is retransmitted after a set timeout.
- **Selective Acknowledgment (SACK):** an optional, negotiated TCP feature that lets receivers acknowledge discontinuous segments, so the sender only resends the missing data.

**Flow control mechanisms**
- **Flow control:** adjusts the rate of data between source and destination so the destination isn't overloaded.
- **Maximum Segment Size (MSS):** the most data a destination can receive per segment. The default IPv4 Ethernet MSS is **1,460 bytes** (1,500-byte MTU - 20-byte IPv4 header - 20-byte TCP header).
- **Sliding window / window size:** the number of bytes accepted at one time, adjusted continuously during transmission.
- **Congestion avoidance:** when expected acknowledgments don't arrive, TCP uses algorithms, timers, and window size reductions to manage congestion.

## 6. UDP Communication

### Low Overhead vs. Reliability
- UDP doesn't set up a connection before sending data.
- Lower overhead: smaller headers and no network management traffic.

### Datagram Reassembly
- UDP doesn't track sequence numbers.
- Out-of-order datagrams aren't re-ordered.
- Lost datagrams aren't retransmitted.
- Data is reassembled in the order received and passed to the application layer.

### UDP Server and Client Processes
- **Servers** listen on well-known or registered ports (e.g., DNS port 53, RADIUS port 1812).
- **Clients** pick a dynamic source port and send to the server's destination port.
- Server replies use the request's **source port** as the reply's **destination port**.

## Exam Reminders
- TCP = connection-oriented, reliable, ordered, flow control. UDP = connectionless, fast, low overhead.
- TCP header = 20 bytes; UDP header = 8 bytes.
- Port ranges: well-known 0-1023, registered 1024-49151, dynamic 49152-65535.
- Socket = IP address + port number.
- Three-way handshake: SYN, SYN-ACK, ACK. Termination: FIN, ACK, FIN, ACK.
- Default Ethernet MSS = 1460 bytes.
- TCP for web, email, and file transfer; UDP for VoIP, DNS, DHCP, TFTP, SNMP.
