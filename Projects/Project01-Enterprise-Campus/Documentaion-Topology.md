# ABC Enterprise Campus Topology

## 1. Network Overview

The network represents a three-building enterprise campus.

```text
                         +------+
                         |  R1  |
                         +--+---+
                            |
                       10.10.254.0/30
                            |
                     +------+------+
                     |  CORE-SW1   |
                     | Layer 3 SW  |
                     +--+----+----+
                        |    |
                 +------+    +------+
                 |                  |
              +--+--+            +--+--+
              | SW-A |            | SW-B |
              +--+--+            +--+--+
                 |                  |
              Admin PCs          Sales PCs

                     +------+
                     | SW-C |
                     +--+---+
                        |
                     IT PCs
```

---

## 2. Device Inventory

| Device   | Type           | Purpose                 |
| -------- | -------------- | ----------------------- |
| R1       | Cisco Router   | Edge/router             |
| CORE-SW1 | Layer 3 Switch | Campus core and routing |
| SW-A     | Layer 2 Switch | Administration          |
| SW-B     | Layer 2 Switch | Sales                   |
| SW-C     | Layer 2 Switch | IT                      |
| PC-A1–A3 | End devices    | Administration          |
| PC-B1–B3 | End devices    | Sales                   |
| PC-C1–C3 | End devices    | IT                      |

---

## 3. VLAN Allocation

| VLAN | Name  | Network         | Building   |
| ---: | ----- | --------------- | ---------- |
|   10 | ADMIN | `10.10.10.0/24` | Building A |
|   20 | SALES | `10.10.20.0/24` | Building B |
|   30 | IT    | `10.10.30.0/24` | Building C |

---

## 4. Physical Connections

### Router to Core

```text
R1 G0/0
     |
     |
CORE-SW1 G1/0/1
```

### Core to Administration

```text
CORE-SW1 G1/0/2
     |
     |
SW-A G0/1
```

### Core to Sales

```text
CORE-SW1 G1/0/3
     |
     |
SW-B G0/1
```

### Core to IT

```text
CORE-SW1 G1/0/4
     |
     |
SW-C G0/1
```

---

## 5. End Device Connections

### Administration

```text
SW-A Fa0/1 → PC-A1
SW-A Fa0/2 → PC-A2
SW-A Fa0/3 → PC-A3
```

### Sales

```text
SW-B Fa0/1 → PC-B1
SW-B Fa0/2 → PC-B2
SW-B Fa0/3 → PC-B3
```

### IT

```text
SW-C Fa0/1 → PC-C1
SW-C Fa0/2 → PC-C2
SW-C Fa0/3 → PC-C3
```

---

## 6. Traffic Flow Example

When PC-A1 communicates with PC-B1:

```text
PC-A1
10.10.10.10
      |
      ↓
SW-A
      |
      ↓
CORE-SW1
10.10.10.1
      |
      ↓
CORE-SW1 routes traffic
      |
      ↓
10.10.20.0/24
      |
      ↓
SW-B
      |
      ↓
PC-B1
10.10.20.10
```

The Layer 3 switch performs the routing between the two IPv4 networks.

---

## 7. Design Goals

The topology demonstrates:

* IPv4 subnetting
* IPv4 routing
* IPv6 addressing
* IPv6 routing
* VLAN segmentation
* Layer 3 switching
* Static routing
* ICMP connectivity testing
* Basic enterprise network documentation
