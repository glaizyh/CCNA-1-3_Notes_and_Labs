# IPv4 VLSM Addressing Plan

## 1. Overview

The ABC Enterprise campus uses the private IPv4 address space:

```text
10.10.0.0/16
```

The network is divided into multiple subnets for the three campus buildings and the routed connection between the core Layer 3 switch and the edge router.

The addressing scheme is designed to provide:

* Separate networks for each building
* Dedicated default gateways
* A point-to-point transit network between the core switch and router
* Room for future expansion

---

## 2. VLSM Requirements

| Network      | Purpose        | Required Hosts |
| ------------ | -------------- | -------------: |
| Building A   | Administration |            254 |
| Building B   | Sales          |            254 |
| Building C   | IT             |            254 |
| Core-R1 Link | Point-to-point |              2 |

The required subnet sizes are:

| Network        | Prefix | Subnet Mask       | Usable Hosts |
| -------------- | ------ | ----------------- | -----------: |
| Administration | `/24`  | `255.255.255.0`   |          254 |
| Sales          | `/24`  | `255.255.255.0`   |          254 |
| IT             | `/24`  | `255.255.255.0`   |          254 |
| Core-R1        | `/30`  | `255.255.255.252` |            2 |

---

## 3. IPv4 Subnet Allocation

### Administration

```text
Network:       10.10.10.0/24
Subnet Mask:   255.255.255.0
First Host:    10.10.10.1
Last Host:     10.10.10.254
Broadcast:     10.10.10.255
```

Default gateway:

```text
10.10.10.1
```

---

### Sales

```text
Network:       10.10.20.0/24
Subnet Mask:   255.255.255.0
First Host:    10.10.20.1
Last Host:     10.10.20.254
Broadcast:     10.10.20.255
```

Default gateway:

```text
10.10.20.1
```

---

### IT

```text
Network:       10.10.30.0/24
Subnet Mask:   255.255.255.0
First Host:    10.10.30.1
Last Host:     10.10.30.254
Broadcast:     10.10.30.255
```

Default gateway:

```text
10.10.30.1
```

---

### Core-Router Transit Network

A `/30` subnet provides exactly two usable addresses.

```text
Network:       10.10.254.0/30
Subnet Mask:   255.255.255.252
Network:       10.10.254.0
First Host:    10.10.254.1
Last Host:     10.10.254.2
Broadcast:     10.10.254.3
```

Assignments:

```text
R1:         10.10.254.1
CORE-SW1:   10.10.254.2
```

---

## 4. Complete IPv4 Addressing Table

| Device   | Interface | Address     | Prefix | Purpose                |
| -------- | --------- | ----------- | ------ | ---------------------- |
| R1       | G0/0      | 10.10.254.1 | /30    | Core connection        |
| CORE-SW1 | G1/0/1    | 10.10.254.2 | /30    | Router connection      |
| CORE-SW1 | VLAN 10   | 10.10.10.1  | /24    | Administration gateway |
| CORE-SW1 | VLAN 20   | 10.10.20.1  | /24    | Sales gateway          |
| CORE-SW1 | VLAN 30   | 10.10.30.1  | /24    | IT gateway             |
| PC-A1    | NIC       | 10.10.10.10 | /24    | Administration         |
| PC-A2    | NIC       | 10.10.10.11 | /24    | Administration         |
| PC-A3    | NIC       | 10.10.10.12 | /24    | Administration         |
| PC-B1    | NIC       | 10.10.20.10 | /24    | Sales                  |
| PC-B2    | NIC       | 10.10.20.11 | /24    | Sales                  |
| PC-B3    | NIC       | 10.10.20.12 | /24    | Sales                  |
| PC-C1    | NIC       | 10.10.30.10 | /24    | IT                     |
| PC-C2    | NIC       | 10.10.30.11 | /24    | IT                     |
| PC-C3    | NIC       | 10.10.30.12 | /24    | IT                     |

---

## 5. Routing

CORE-SW1 directly connects to all three campus networks:

```text
10.10.10.0/24
10.10.20.0/24
10.10.30.0/24
```

R1 reaches these networks through CORE-SW1.

Static routes on R1:

```text
ip route 10.10.10.0 255.255.255.0 10.10.254.2
ip route 10.10.20.0 255.255.255.0 10.10.254.2
ip route 10.10.30.0 255.255.255.0 10.10.254.2
```

---

## 6. Verification Commands

On CORE-SW1:

```text
show ip interface brief
show ip route
show vlan brief
```

On R1:

```text
show ip interface brief
show ip route
```

From a PC:

```text
ping 10.10.10.1
ping 10.10.20.10
ping 10.10.30.10
```

---

