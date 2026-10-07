# Module 5: ACLs for IPv4 Configuration

## 1. Configure Standard IPv4 ACLs

### ACL Creation Workflow
**Best practice:** plan in a text editor, write out the details, include remarks, copy and paste onto the device, and test thoroughly before using it in production.

### Numbered Standard IPv4 ACL Syntax
```
Router(config)# access-list access-list-number {deny | permit | remark text} source [source-wildcard] [log]
```
- **Number range:** `1-99` or `1300-1999` (standard IPv4).
- **Optional parameters:**
  - `remark text`: documents the purpose.
  - `source-wildcard`: a 32-bit wildcard mask applied to the source IP.
  - `log`: sends a syslog message when the ACE matches.
- **Removal:** `no access-list access-list-number`

### Named Standard IPv4 ACL Syntax
```
Router(config)# ip access-list standard access-list-name
```
- Names are **case-sensitive** and alphanumeric. Capital letters are recommended so the name stands out in `running-config`.
- **Removal:** `no ip access-list standard access-list-name`

### Applying ACLs to Interfaces
```
Router(config-if)# ip access-group {access-list-number | access-list-name} {in | out}
```
- **Removal:** `no ip access-group {access-list-number | access-list-name} {in | out}`

### Configuration Examples

**Numbered example**
```
R1(config)# access-list 10 remark ACE permits ONLY host 192.168.10.10 to the internet
R1(config)# access-list 10 permit host 192.168.10.10
R1(config)# access-list 10 remark ACE permits all host in LAN 2
R1(config)# access-list 10 permit 192.168.20.0 0.0.0.255
R1(config)# interface Serial 0/1/0
R1(config-if)# ip access-group 10 out
```

**Named example**
```
R1(config)# ip access-list standard PERMIT-ACCESS
R1(config-std-nacl)# remark ACE permits host 192.168.10.10
R1(config-std-nacl)# permit host 192.168.10.10
R1(config-std-nacl)# remark ACE permits all hosts in LAN 2
R1(config-std-nacl)# permit 192.168.20.0 0.0.0.255
R1(config-std-nacl)# exit
R1(config)# interface Serial 0/1/0
R1(config-if)# ip access-group PERMIT-ACCESS out
```

### Verification Commands

| Command | Shows |
|---------|-------|
| `show access-lists` / `show ip access-lists` | All configured ACLs, with their sequence numbers |
| `show ip interface [interface-id]` | Whether an ACL is applied inbound or outbound on the interface |
| `show running-config \| section access-list` | The configured ACL lines |

## 2. Modify IPv4 ACLs

### Modification Methods
1. **Text editor method:** copy the ACL from `show run`, edit it in a text editor, delete the original ACL with `no access-list`, and paste the new version into the CLI.
2. **Sequence number method:** change or insert ACEs in the ACL's configuration mode.
   - *Insert:* add an entry with a sequence number between the existing ones (e.g., `15 deny 192.168.10.5`).
   - *Delete:* remove an entry with `no [sequence-number]` (e.g., `no 10`).
   - *Overwriting:* an existing sequence number can't be overwritten directly. Delete the old statement first.

### ACL Hit Counters and Statistics
- `show access-lists` shows a **match counter** for each explicitly defined ACE.
- The **implicit deny** doesn't show statistics by default. To track dropped packets, add `deny any` at the end of the ACL.
- `clear access-list counters [access-list-name]` resets the statistics.

## 3. Secure VTY Ports with a Standard IPv4 ACL

### The `access-class` Command
- **Purpose:** restricts remote administrative access (SSH/Telnet) to authorized management source IP addresses.
- **Syntax:**
  ```
  Router(config-line)# access-class {access-list-number | access-list-name} {in | out}
  ```
- Use `in` to restrict incoming SSH/Telnet connection attempts to the VTY lines.
- It is applied under the **line** (`line vty`), not under an interface, so it uses `access-class` instead of `ip access-group`.

### Secure VTY Access Example
```
R1(config)# username ADMIN secret class
R1(config)# ip access-list standard ADMIN-HOST
R1(config-std-nacl)# remark This ACL secures incoming vty lines
R1(config-std-nacl)# permit 192.168.10.10
R1(config-std-nacl)# deny any
R1(config-std-nacl)# exit
R1(config)# line vty 0 4
R1(config-line)# login local
R1(config-line)# transport input telnet
R1(config-line)# access-class ADMIN-HOST in
R1(config-line)# end
```
This example uses Telnet, which sends data in plaintext. In practice, use `transport input ssh` (see CCNA 1 Module 16 for the SSH setup).

## 4. Configure Extended IPv4 ACLs

### Extended ACL Features and Number Ranges
- **Can filter on:** source IP, destination IP, protocol type (IP, TCP, UDP, ICMP), source and destination ports, and connection status.
- **Number ranges:** `100-199` or `2000-2699`.
- **Syntax:**
  ```
  Router(config)# access-list access-list-number {deny | permit} protocol source source-wildcard [operator port] destination destination-wildcard [operator port] [established]
  ```

### Protocols and Port Numbers
- **Common protocols:** `ip`, `tcp`, `udp`, `icmp`.

| Port match operator | Meaning |
|---------------------|---------|
| `eq` | Equal |
| `neq` | Not equal |
| `gt` | Greater than |
| `lt` | Less than |

| Service | Port | Keyword |
|---------|------|---------|
| FTP data | 20 | `ftp-data` |
| FTP control | 21 | `ftp` |
| SSH | 22 | |
| Telnet | 23 | `telnet` |
| DNS | 53 | `domain` |
| HTTP | 80 | `www` |
| HTTPS | 443 | |

### Stateful Filtering with the `established` Keyword
- **Purpose:** lets returning TCP traffic back in for connections that started inside the private network.
- **How it works:** matches returning TCP segments that have the `ACK` or `RST` flag bit set.

```
R1(config)# access-list 120 permit tcp any 192.168.10.0 0.0.0.255 established
R1(config)# interface g0/0/0
R1(config-if)# ip access-group 120 out
```

### Named Extended IPv4 ACL Example
```
R1(config)# ip access-list extended SURFING
R1(config-ext-nacl)# remark Permits inside HTTP and HTTPS traffic
R1(config-ext-nacl)# permit tcp 192.168.10.0 0.0.0.255 any eq 80
R1(config-ext-nacl)# permit tcp 192.168.10.0 0.0.0.255 any eq 443
R1(config-ext-nacl)# exit

R1(config)# ip access-list extended BROWSING
R1(config-ext-nacl)# remark Only permit returning HTTP and HTTPS traffic
R1(config-ext-nacl)# permit tcp any 192.168.10.0 0.0.0.255 established
R1(config-ext-nacl)# exit

R1(config)# interface g0/0/0
R1(config-if)# ip access-group SURFING in
R1(config-if)# ip access-group BROWSING out
```

## Exam Reminders
- Numbered standard: `access-list 1-99`; named standard: `ip access-list standard NAME`.
- Apply to an interface with `ip access-group <name|number> in|out`; apply to VTY lines with `access-class`.
- Use `remark` to document ACEs, and `no access-list` (or `no ip access-list ...`) to remove an ACL.
- Edit with sequence numbers: insert (e.g., `15 deny ...`), delete with `no <sequence>`. You can't overwrite a sequence number.
- Add `deny any` at the end to see hit counts for dropped traffic; reset with `clear access-list counters`.
- Extended ACL syntax: `permit|deny protocol source wildcard destination wildcard [operator port]`.
- Operators: `eq`, `neq`, `gt`, `lt`. Ports: FTP 20/21, SSH 22, Telnet 23, DNS 53, HTTP 80, HTTPS 443.
- `established` matches TCP segments with ACK or RST set (return traffic).
- Verify with `show access-lists` and `show ip interface`.
