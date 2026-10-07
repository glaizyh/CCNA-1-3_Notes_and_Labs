# Module 4: ACL Concepts

## 1. Purpose of ACLs

### What Is an ACL?
- **Definition:** a series of Cisco IOS commands that filters network packets based on information in the packet header.
- **Access Control Entries (ACEs):** the ordered list of `permit` or `deny` statements (usually just called ACL statements).
- **Packet filtering:** comparing the packet header fields with each ACE in order, to decide whether to forward or drop the packet.
- **Default state:** routers have **no ACLs** configured by default.

### Router Tasks That Need ACLs
- Limit network traffic to improve performance.
- Control traffic flow and give basic network access security.
- Filter traffic by type, or screen hosts to permit or deny access to network services.
- Give priority handling to specific classes of network traffic.

### Packet Filtering and OSI Layers

| ACL type | Filters at | Based on |
|----------|-----------|----------|
| **Standard ACL** | Layer 3 | **Source IPv4 address only** |
| **Extended ACL** | Layer 3 and Layer 4 | Source and destination IPv4 addresses, TCP/UDP protocols, port numbers, and optional protocol flags |

### ACL Directions

| Direction | When it filters | Notes |
|-----------|-----------------|-------|
| **Inbound** | Before packets are routed to an outbound interface | Saves routing lookup overhead if packets are discarded |
| **Outbound** | After routing is done, whatever the ingress interface was | |

ACLs do **not** filter traffic that comes from the local router itself.

### Inbound ACL Processing Order
1. The router takes the source IPv4 address from the packet header.
2. It compares the address with the ACEs in order, starting at the top.
3. On a match, it carries out the instruction (`permit` or `deny`) and **stops checking the remaining ACEs**.
4. **Implicit deny:** every ACL ends with a hidden `deny` that drops any traffic that didn't match. It isn't shown in `show running-config`.
5. **Requirement:** every ACL must contain at least one `permit` statement. Otherwise the implicit deny blocks all traffic.

## 2. Wildcard Masks in ACLs

### Wildcard Mask Rules
- A 32-bit mask used in an ACE to say which bits of the IP address to match and which to ignore.
- Bit **`0`:** match the corresponding bit exactly.
- Bit **`1`:** ignore the corresponding bit.

### Common Wildcard Mask Calculations
- **Shortcut:** subtract the subnet mask from `255.255.255.255`.

| Match | Wildcard mask | Example |
|-------|---------------|---------|
| A single host | `0.0.0.0` | `access-list 10 permit 192.168.1.1 0.0.0.0` |
| A /24 subnet | `0.0.0.255` | 255.255.255.255 - 255.255.255.0 = 0.0.0.255 |
| A /28 subnet | `0.0.0.15` | 255.255.255.255 - 255.255.255.240 = 0.0.0.15 |

### Wildcard Mask Keywords
- **`host`:** takes the place of the `0.0.0.0` wildcard mask (e.g., `permit host 192.168.1.1`).
- **`any`:** takes the place of an address with the `255.255.255.255` wildcard mask (`0.0.0.0 255.255.255.255`), so it matches every address.

## 3. Guidelines for ACL Creation

### Interface ACL Limits
An interface can have at most **4 ACLs** in total (when dual-stacked), one per protocol and direction:
- One outbound IPv4 ACL
- One inbound IPv4 ACL
- One outbound IPv6 ACL
- One inbound IPv6 ACL

### Best Practices
- Base ACLs on the organization's security policies.
- Draft the ACL logic before applying it, to avoid locking yourself out by mistake.
- Use a text editor to write and edit ACLs, so you build a reusable library of configurations.
- Use the `remark` command to document what each ACE is for.
- Test ACLs in a development network before deploying them.

## 4. Types of IPv4 ACLs

### Numbered vs. Named ACLs

| Type | Ranges |
|------|--------|
| **Standard numbered** | `1-99` and expanded `1300-1999` |
| **Extended numbered** | `100-199` and expanded `2000-2699` |
| **Named** | Preferred method; uses descriptive names (e.g., `ip access-list extended FTP-FILTER`) |

### Placement Rules for ACLs

| ACL type | Place it | Why |
|----------|----------|-----|
| **Extended** | As close to the **source** of the traffic as possible | Unwanted packets are filtered before they use up link bandwidth |
| **Standard** | As close to the **destination** as possible | A standard ACL only checks the source address, so placing it earlier could block valid traffic to other destinations |

Placement is also influenced by network administrative control, link bandwidth, and ease of configuration.

## Exam Reminders
- ACE = one permit/deny line. ACLs are checked top to bottom, and the first match wins.
- Every ACL ends with an **implicit deny**, so it needs at least one `permit`.
- Standard = source IPv4 only (Layer 3). Extended = source and destination, protocols, and ports (Layers 3 and 4).
- Wildcard mask: `0` = match, `1` = ignore. Wildcard = 255.255.255.255 minus the subnet mask.
- `host` = wildcard 0.0.0.0; `any` = 0.0.0.0 255.255.255.255.
- Max 4 ACLs per interface: IPv4 in/out and IPv6 in/out.
- Standard numbered: 1-99 and 1300-1999. Extended numbered: 100-199 and 2000-2699.
- Extended ACLs go near the **source**; standard ACLs go near the **destination**.
- ACLs don't filter traffic that originates from the router itself.
