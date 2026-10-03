# Module 15: Application Layer

## 1. Application, Presentation, and Session

### Application Layer
- The upper three OSI layers (**Application, Presentation, Session**) make up the **TCP/IP Application layer**.
- It provides the interface between the applications used to communicate and the network that carries the messages.
- Well-known protocols: HTTP, FTP, TFTP, IMAP, DNS.

### Presentation and Session Layers

**Presentation layer functions**
- Format (present) data at the source in a form the destination device can use.
- Compress data so the destination device can decompress it.
- Encrypt data for transmission and decrypt it on receipt.
- Examples: MKV, MPG, MOV, GIF, JPG, PNG.

**Session layer functions**
- Creates and maintains dialogs between source and destination applications.
- Handles the exchange of information to start dialogs, keep them active, and restart sessions that were interrupted or idle for a long time.

### TCP/IP Application Layer Protocols
- Specify the format and control information needed for many common internet communication functions.
- Used by both the source and destination devices during a communication session.
- The protocols implemented on the source and destination hosts must be **compatible** for communication to work.

| Protocol | Function | Port |
|----------|----------|------|
| **DNS** (Domain Name System) | Translates domain names into IP addresses | TCP/UDP 53 |
| **DHCP** (Dynamic Host Configuration Protocol) | Dynamically assigns IP addresses that can be reused when no longer needed | UDP client 68, server 67 |
| **HTTP** (Hypertext Transfer Protocol) | Rules for exchanging multimedia files on the World Wide Web | TCP 80, 8080 |

## 2. Peer-to-Peer

### Client-Server Model
- Client and server **processes** are in the application layer.
- **Client:** the device requesting information.
- **Server:** the device responding to the request.
- Application layer protocols describe the format of the requests and responses between clients and servers.

### Peer-to-Peer Networks
- Two or more computers share resources (printers, files) over a network **without a dedicated server**.
- Every connected end device (a **peer**) can act as both a server and a client.
- Client and server roles are set **per request**.

### Peer-to-Peer Applications
- Let a device act as both client and server in the same communication.
- Some P2P applications are hybrid: each peer asks an **index server** where a resource is stored on another peer.
- Common P2P applications: BitTorrent, Direct Connect, eDonkey, Freenet.

## 3. Web and Email Protocols

### HTTP and HTML Process
1. The browser reads the three parts of the URL: the protocol/scheme (`http`), the server name (`www.cisco.com`), and the requested file (`index.html`).
2. The browser asks a name server to convert the domain name into a numeric IP address. The client then sends a **GET** request to the server for the file.
3. The server sends the HTML code for the page to the browser.
4. The browser interprets the HTML and formats the page for the browser window.

### HTTP and HTTPS Messages

| Message | Purpose |
|---------|---------|
| **GET** | Client request for data (e.g., an HTML page from a web server) |
| **POST** | Uploads data files to the web server, such as form data |
| **PUT** | Uploads resources or content to the web server, such as an image |
| **HTTPS** | Secure communication across the internet, since HTTP is not secure |

### Email Protocols
Email uses the **store-and-forward** method: messages are sent, stored, and retrieved across a network, and kept in databases on mail servers.

| Protocol | Role | Details |
|----------|------|---------|
| **SMTP** (Simple Mail Transfer Protocol) | **Sends** mail | Connects to the server's SMTP process on well-known **TCP port 25**. Places the message in a local account or forwards it to another mail server. Spools messages if the destination server is offline or busy. Requires a message header and body |
| **POP** (Post Office Protocol) | **Retrieves** mail | Listens passively on **TCP port 110** for client connection requests. Downloads messages to the client and then **deletes them from the server**. Not recommended for small businesses that need centralized backups |
| **IMAP** (Internet Message Access Protocol) | **Retrieves** mail | Downloads **copies** of messages to the client while the originals stay on the server until manually deleted. The server synchronizes the user's deletions |

## 4. IP Addressing Services

### Domain Name Service (DNS)
- Converts **names to numeric IP addresses**, so people can use simple, recognizable names (FQDNs) instead of numbers.
- Defines an automated service that matches resource names with numeric network addresses.

### DNS Resource Records

| Record | Meaning |
|--------|---------|
| **A** | An end device's IPv4 address |
| **NS** | An authoritative name server |
| **AAAA** | An end device's IPv6 address (pronounced "quad-A") |
| **MX** | A mail exchange record |

### DNS Message Format
DNS uses one message format with these sections:
- **Question:** the question for the name server
- **Answer:** resource records answering the question
- **Authority:** resource records pointing toward an authority
- **Additional:** resource records holding additional information

### DNS Hierarchy
1. DNS uses a **hierarchical** system for name resolution.
2. **Zones:** each DNS server manages name-to-IP mappings for its own section of the database, and forwards requests it can't resolve to other zone servers.
3. **Top-level domain (TLD) examples:** `.com` (business/industry), `.org` (non-profit), `.au` (Australia).

### The `nslookup` Command
- An OS utility for manually querying the configured DNS servers to resolve hostnames.
- Used to troubleshoot name resolution problems and verify the status of name servers.

### Dynamic Host Configuration Protocol (DHCP)
- Automates the assignment of IPv4 addresses, subnet masks, gateways, and other network parameters.
- Assigns (**leases**) addresses dynamically from a configured pool.
- Used for general-purpose hosts (end-user devices); **static** addressing is used for network devices (routers, switches, servers, printers).

**DHCPv4 four-step process**

| Step | Message | Description |
|------|---------|-------------|
| 1 | **DHCPDISCOVER** | The client broadcasts to find available DHCP servers |
| 2 | **DHCPOFFER** | A server offers a lease to the client |
| 3 | **DHCPREQUEST** | The client names the server and lease offer it accepts |
| 4 | **DHCPACK** | The server acknowledges that the lease is final |

`DHCPNAK` is sent if the offer is no longer valid.

**DHCPv6 messages:** SOLICIT, ADVERTISE, INFORMATION REQUEST, REPLY. DHCPv6 does **not** provide a default gateway address; that comes from the Router Advertisement.

## 5. File Sharing Services

### File Transfer Protocol (FTP)
Transfers data between a client and a server.
- **Control connection:** the client opens the first connection for control traffic, using **TCP port 21**.
- **Data connection:** the client opens a second connection for data traffic, using **TCP port 20**.
- Data can be downloaded (pulled) from the server or uploaded (pushed) to it.

### Server Message Block (SMB)
- A client/server, request-response file sharing protocol.
- SMB messages are used to:
  - Start, authenticate, and end sessions
  - Control file and printer access
  - Let an application send or receive messages to or from another device
- Clients keep a **long-term connection** to servers, so resources can be used as if they were local.

## Exam Reminders
- OSI Application + Presentation + Session = TCP/IP Application layer.
- Ports: DNS 53, DHCP 67/68 (UDP), HTTP 80, SMTP 25, POP 110, FTP 21 (control) and 20 (data).
- SMTP sends; POP and IMAP retrieve. POP deletes from the server; IMAP keeps a copy.
- DHCP steps: Discover, Offer, Request, Acknowledge (DORA).
- DNS records: A (IPv4), AAAA (IPv6), NS (name server), MX (mail).
- GET = request, POST = send form data, PUT = upload content.
- DHCPv6 doesn't give a default gateway; the RA does.
