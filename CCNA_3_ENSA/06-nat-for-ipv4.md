#  Module 6: NAT for IPv4

## 1. NAT Characteristics

### IPv4 Address Space and RFC 1918 Private Addresses
Private IPv4 addresses can't be routed over the public internet and are reserved for internal use. **NAT** translates private IPv4 addresses into public IPv4 addresses.

| Class | Range | Prefix |
|-------|-------|--------|
| **A** | 10.0.0.0 to 10.255.255.255 | 10.0.0.0/8 |
| **B** | 172.16.0.0 to 172.31.255.255 | 172.16.0.0/12 |
| **C** | 192.168.0.0 to 192.168.255.255 | 192.168.0.0/16 |

### What Is NAT?
- **Primary purpose:** conserves public IPv4 addresses.
- **Placement:** works at the border of a **stub network** (a network with a single exit point to the internet).
- **Basic flow:** when an inside host sends traffic to an outside destination, the NAT border router translates the inside private address into a globally routable public address.

### NAT Address Terminology
NAT terms are defined relative to the device whose address is being translated:
- **Inside address:** the address of the device being translated by NAT.
- **Outside address:** the address of the destination device.
- **Local address:** any address as it appears on the **inside** part of the network.
- **Global address:** any address as it appears on the **outside** part of the network.

### The Four NAT Address Types

| Type | Meaning | Example |
|------|---------|---------|
| **Inside local** | The source address as seen from inside the local network (usually an RFC 1918 private address) | 192.168.10.10 |
| **Inside global** | The source address as seen from the outside network, after translation (a globally routable public address) | 209.165.200.226 |
| **Outside local** | The destination address as seen from inside the local network (usually the same as the outside global address) | |
| **Outside global** | The destination address as seen from the outside network (the destination device's globally routable public address) | 209.165.201.1 |

## 2. Types of NAT

### Static NAT
- **Definition:** a permanent **1-to-1** mapping between an inside local address and an inside global address, configured manually by an administrator.
- **Main use:** web servers or other internal resources that must accept connections started from the internet.
- **Requirement:** one dedicated public IPv4 address for every internal server mapped.

### Dynamic NAT
- **Definition:** uses a **pool** of public IPv4 addresses and hands them out first come, first served.
- **Operation:** when an inside host asks for internet access, the router gives it an unused public address from the pool.
- **Limitation:** the mapping is 1-to-1 while it is active. If every pool address is in use, more hosts must wait until a lease expires.

### Port Address Translation (PAT) / NAT Overload
- **Definition:** maps many private IPv4 addresses to a **single** public IPv4 address (or a small pool) by tracking TCP/UDP source port numbers.
- **Port multiplexing:** PAT keeps the original source port when it's available. If another session already uses it, PAT assigns the first available port from the right group (`0-511`, `512-1023`, or `1024-65535`).
- **Packets without Layer 4 headers (ICMPv4):** PAT uses ICMP query IDs to match echo requests with echo replies.

### NAT vs. PAT

| Feature | Static / Dynamic NAT | PAT (NAT overload) |
|---------|----------------------|--------------------|
| **Address mapping** | 1-to-1 between inside local and inside global addresses | Many-to-1 (or many-to-few) |
| **Header fields changed** | IPv4 address only | IPv4 address **and** TCP/UDP port numbers |
| **Public IP needed** | A unique public IP for each simultaneous inside host | One public IP can be shared by thousands of hosts |

## 3. NAT Advantages and Disadvantages

### Advantages of NAT/PAT
- Conserves registered public IPv4 addresses.
- Makes internal addressing more flexible and consistent.
- Lets you change service providers without re-addressing internal hosts.
- Hides private IP addresses from outside reconnaissance.

### Disadvantages of NAT
- Adds delay to packet forwarding.
- Breaks end-to-end addressing and IP traceability.
- Complicates tunneling protocols such as IPsec, because changing headers breaks integrity checks.
- Disrupts services that need incoming connections to be started from outside, or stateless UDP traffic, unless static mappings exist.

## 4. Configure Static NAT

### Configuration Steps
1. **Define the static translation:**
   `ip nat inside source static [inside-local-ip] [inside-global-ip]`
2. **Mark the inside and outside interfaces:**
   - `interface [type/number]` then `ip nat inside`
   - `interface [type/number]` then `ip nat outside`

### Example
```
R2(config)# ip nat inside source static 192.168.10.254 209.165.201.5
R2(config)# interface serial 0/1/0
R2(config-if)# ip nat inside
R2(config-if)# exit
R2(config)# interface serial 0/1/1
R2(config-if)# ip nat outside
```

## 5. Configure Dynamic NAT

### Configuration Steps
1. **Define the pool of public addresses:**
   `ip nat pool [pool-name] [start-ip] [end-ip] netmask [netmask]`
2. **Define the allowed source addresses with a standard ACL:**
   `access-list [acl-number] permit [source-ip] [wildcard-mask]`
3. **Bind the ACL to the pool:**
   `ip nat inside source list [acl-number] pool [pool-name]`
4. **Assign the inside and outside interfaces:** apply `ip nat inside` and `ip nat outside` on the right interfaces.

### Example
```
R2(config)# ip nat pool NAT-POOL1 209.165.200.226 209.165.200.240 netmask 255.255.255.224
R2(config)# access-list 1 permit 192.168.0.0 0.0.255.255
R2(config)# ip nat inside source list 1 pool NAT-POOL1
R2(config)# interface serial 0/1/0
R2(config-if)# ip nat inside
R2(config-if)# interface serial 0/1/1
R2(config-if)# ip nat outside
```

## 6. Configure Port Address Translation (PAT)

### PAT Using a Single Exit Interface IP Address
Add the `overload` keyword to tie the translation to an exit interface.
```
R2(config)# access-list 1 permit 192.168.0.0 0.0.255.255
R2(config)# ip nat inside source list 1 interface serial 0/1/1 overload
R2(config)# interface serial 0/1/0
R2(config-if)# ip nat inside
R2(config-if)# interface serial 0/1/1
R2(config-if)# ip nat outside
```

### PAT Using an Address Pool
Add `overload` to tie the translation to a pool.
```
R2(config)# ip nat pool NAT-POOL2 209.165.200.226 209.165.200.240 netmask 255.255.255.224
R2(config)# access-list 1 permit 192.168.0.0 0.0.255.255
R2(config)# ip nat inside source list 1 pool NAT-POOL2 overload
R2(config)# interface serial 0/1/0
R2(config-if)# ip nat inside
R2(config-if)# interface serial 0/1/1
R2(config-if)# ip nat outside
```

### Verification and Maintenance Commands

| Command | Shows or does |
|---------|---------------|
| `show ip nat translations` | All active NAT/PAT translation entries |
| `show ip nat translations verbose` | Adds timer information, age, and map IDs to the translations |
| `show ip nat statistics` | Active translations, hit/miss counters, pool allocation percentages, and the inside and outside interfaces |
| `clear ip nat translation *` | Clears all dynamic translation entries from the NAT table |
| `ip nat translation timeout [seconds]` | Changes the default 24-hour timeout for dynamic entries |

## 7. NAT64

### IPv6 and NAT
- **IPv6 design intent:** IPv6 was designed to remove the need for NAT/PAT, thanks to its huge global unicast address space.
- **Unique Local Addresses (ULAs):** like IPv6 private addresses (`fc00::/7`), but meant only for communication inside a site, not for conserving addresses or hiding them.

### NAT64 Protocol Translation
- **Function:** translates IPv6 packet headers to IPv4 headers (and back), so IPv6-only and IPv4-only networks can communicate.
- **Migration tool:** a temporary transition mechanism during IPv6 migration, not a permanent addressing strategy.

## Exam Reminders
- NAT terms: inside local (private), inside global (public), outside local, outside global.
- Static NAT = permanent 1-to-1 (for servers). Dynamic NAT = pool, first come first served. PAT = many-to-one using port numbers.
- PAT = `overload`. Interfaces need `ip nat inside` and `ip nat outside`.
- Dynamic NAT/PAT uses a standard ACL to say **which** inside addresses get translated.
- `show ip nat translations` shows what's being translated; `clear ip nat translation *` clears it.
- Default dynamic translation timeout = 24 hours.
- NAT hides private addresses but breaks end-to-end traceability and complicates IPsec.
- IPv6 is designed to avoid NAT; NAT64 is only for IPv6-to-IPv4 transition.
