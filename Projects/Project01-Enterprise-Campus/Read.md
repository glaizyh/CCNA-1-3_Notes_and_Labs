# Enterprise IPv4 & IPv6 Campus Network

A three-building enterprise campus network designed and simulated using Cisco Packet Tracer.

This project demonstrates basic enterprise networking concepts including:

* IPv4 addressing and subnetting
* IPv6 addressing
* VLAN segmentation
* Layer 3 switching
* Static routing
* IPv4 and IPv6 connectivity
* Basic Cisco IOS configuration
* Network verification and troubleshooting

## Project Topology

```text
                         +------+
                         |  R1  |
                         +--+---+
                            |
                            |
                     +------+------+
                     |  CORE-SW1  |
                     | Layer 3 SW |
                     +--+----+----+
                        |    |    |
                        |    |    |
                     +--+  +-+--+ +--+--+
                     |SW-A| |SW-B| |SW-C|
                     +--+-+ +--+-+ +--+-+
                        |     |     |
                      Admin  Sales   IT
                       PCs    PCs    PCs

```

## Installation

No programming package installation is required.

You need:

- Cisco Packet Tracer
- A text/Markdown editor such as VS Code or Notepad++

Open the Packet Tracer project:

[Download/Open 01_Enterprise_Campus.pkt](./01_Enterprise_Campus.pkt)

## Usage

Open `01-Enterprise-Campus.pkt` in Cisco Packet Tracer.

After opening the project, verify that all devices are connected and powered on.

You can test connectivity from any PC using the Command Prompt.

### IPv4 Test

From an Administration PC:

```text
ping 10.10.20.10
```

Expected result:

```text
Reply from 10.10.20.10
```

### IPv6 Test

From an Administration PC:

```text
ping 2001:db8:acad:20::10
```

Expected result:

```text
Reply from 2001:db8:acad:20::10
```

## IP Addressing

### IPv4

| Network          | Purpose        | Gateway            |
| ---------------- | -------------- | ------------------ |
| `10.10.10.0/24`  | Administration | `10.10.10.1`       |
| `10.10.20.0/24`  | Sales          | `10.10.20.1`       |
| `10.10.30.0/24`  | IT             | `10.10.30.1`       |
| `10.10.254.0/30` | R1 ↔ CORE-SW1  | `10.10.254.1 / .2` |

### IPv6

| Network                 | Purpose        | Gateway               |
| ----------------------- | -------------- | --------------------- |
| `2001:db8:acad:10::/64` | Administration | `2001:db8:acad:10::1` |
| `2001:db8:acad:20::/64` | Sales          | `2001:db8:acad:20::1` |
| `2001:db8:acad:30::/64` | IT             | `2001:db8:acad:30::1` |

## VLANs

| VLAN | Name  | Network         |
| ---: | ----- | --------------- |
|   10 | ADMIN | `10.10.10.0/24` |
|   20 | SALES | `10.10.20.0/24` |
|   30 | IT    | `10.10.30.0/24` |

## Devices

| Device   | Role                         |
| -------- | ---------------------------- |
| R1       | Edge Router                  |
| CORE-SW1 | Layer 3 Core Switch          |
| SW-A     | Administration Access Switch |
| SW-B     | Sales Access Switch          |
| SW-C     | IT Access Switch             |
| PC-A1–A3 | Administration PCs           |
| PC-B1–B3 | Sales PCs                    |
| PC-C1–C3 | IT PCs                       |

## Verification Commands

### CORE-SW1

```text
show ip interface brief
show ip route
show ipv6 interface brief
show ipv6 route
show vlan brief
```

### R1

```text
show ip interface brief
show ip route
```

### Access Switches

```text
show vlan brief
show interfaces status
```

## Expected Results

The completed network should provide:

* Administration PCs communicating with the Administration gateway.
* Sales PCs communicating with the Sales gateway.
* IT PCs communicating with the IT gateway.
* IPv4 communication between all three networks.
* IPv6 communication between all three networks.
* R1 successfully communicating with CORE-SW1.
* Static routes on R1 pointing toward the campus networks.

## Prerequisites

Before starting this project, you should have a basic understanding of:

* IPv4 addresses
* Subnet masks
* CIDR notation
* IPv6 addresses
* Default gateways
* VLANs
* Cisco IOS CLI
* ICMP/Ping
* Basic Layer 2 and Layer 3 networking

## Learning Objectives

After completing this project, I should be able to:

1. Design a basic enterprise campus topology.
2. Calculate and assign IPv4 subnets.
3. Create an IPv6 addressing plan.
4. Configure VLANs.
5. Configure a Layer 3 switch.
6. Configure IPv4 and IPv6 gateways.
7. Configure static routes.
8. Verify network connectivity.
9. Troubleshoot basic connectivity problems.
10. Document a Cisco network professionally.

## Status

```text
Project 1 — Enterprise IPv4 & IPv6 Campus

[X] Topology completed
[X] IPv4 addressing configured
[X] IPv6 addressing configured
[X] VLANs configured
[X] Layer 3 routing configured
[X] Static routes configured
[X] IPv4 connectivity verified
[X] IPv6 connectivity verified
[X] Configurations documented
[X] Project completed
```
