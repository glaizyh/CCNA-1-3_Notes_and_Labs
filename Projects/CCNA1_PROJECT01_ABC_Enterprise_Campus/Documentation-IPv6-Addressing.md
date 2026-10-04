# IPv6 Unicast Addressing Plan

## 1. Overview

The campus IPv6 address space is:

```text
2001:db8:acad::/48
```

The `/48` network is divided into separate `/64` networks.

A `/64` is the standard subnet size used for typical IPv6 LANs.

---

## 2. IPv6 Network Allocation

| Building       | IPv6 Network            | Gateway               |
| -------------- | ----------------------- | --------------------- |
| Administration | `2001:db8:acad:10::/64` | `2001:db8:acad:10::1` |
| Sales          | `2001:db8:acad:20::/64` | `2001:db8:acad:20::1` |
| IT             | `2001:db8:acad:30::/64` | `2001:db8:acad:30::1` |

---

## 3. Administration IPv6 Network

```text
Network:
2001:db8:acad:10::/64
```

Gateway:

```text
2001:db8:acad:10::1/64
```

Hosts:

```text
PC-A1:
2001:db8:acad:10::10/64

PC-A2:
2001:db8:acad:10::11/64

PC-A3:
2001:db8:acad:10::12/64
```

---

## 4. Sales IPv6 Network

```text
Network:
2001:db8:acad:20::/64
```

Gateway:

```text
2001:db8:acad:20::1/64
```

Hosts:

```text
PC-B1:
2001:db8:acad:20::10/64

PC-B2:
2001:db8:acad:20::11/64

PC-B3:
2001:db8:acad:20::12/64
```

---

## 5. IT IPv6 Network

```text
Network:
2001:db8:acad:30::/64
```

Gateway:

```text
2001:db8:acad:30::1/64
```

Hosts:

```text
PC-C1:
2001:db8:acad:30::10/64

PC-C2:
2001:db8:acad:30::11/64

PC-C3:
2001:db8:acad:30::12/64
```

---

## 6. Complete IPv6 Address Table

| Device   | Interface | IPv6 Address              |
| -------- | --------- | ------------------------- |
| CORE-SW1 | VLAN 10   | `2001:db8:acad:10::1/64`  |
| CORE-SW1 | VLAN 20   | `2001:db8:acad:20::1/64`  |
| CORE-SW1 | VLAN 30   | `2001:db8:acad:30::1/64`  |
| PC-A1    | NIC       | `2001:db8:acad:10::10/64` |
| PC-A2    | NIC       | `2001:db8:acad:10::11/64` |
| PC-A3    | NIC       | `2001:db8:acad:10::12/64` |
| PC-B1    | NIC       | `2001:db8:acad:20::10/64` |
| PC-B2    | NIC       | `2001:db8:acad:20::11/64` |
| PC-B3    | NIC       | `2001:db8:acad:20::12/64` |
| PC-C1    | NIC       | `2001:db8:acad:30::10/64` |
| PC-C2    | NIC       | `2001:db8:acad:30::11/64` |
| PC-C3    | NIC       | `2001:db8:acad:30::12/64` |

---

## 7. IPv6 Routing

IPv6 routing is enabled on CORE-SW1 with:

```text
ipv6 unicast-routing
```

The switch routes between:

```text
2001:db8:acad:10::/64
2001:db8:acad:20::/64
2001:db8:acad:30::/64
```

---

## 8. IPv6 Verification

On CORE-SW1:

```text
show ipv6 interface brief
show ipv6 route
```

From a PC:

```text
ping 2001:db8:acad:10::1
ping 2001:db8:acad:20::10
ping 2001:db8:acad:30::10
```

---
