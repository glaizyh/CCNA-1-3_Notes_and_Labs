# Configuration R1

```text
enable
configure terminal

!
! Basic identification
!
hostname R1

!
! Disable DNS lookup to prevent CLI delays from mistyped commands
!
no ip domain-lookup

!
! Core connection
!
interface gigabitEthernet 0/0
 description Connection-to-CORE-SW1
 ip address 10.10.254.1 255.255.255.252
 no shutdown
 exit

!
! Static routes to campus networks
!
ip route 10.10.10.0 255.255.255.0 10.10.254.2
ip route 10.10.20.0 255.255.255.0 10.10.254.2
ip route 10.10.30.0 255.255.255.0 10.10.254.2

!
! Save configuration
!
end
copy running-config startup-config
```

---

# Configuration-CORE-SW1

```text
enable
configure terminal

!
! Basic identification
!
hostname CORE-SW1
no ip domain-lookup

!
! Enable Layer 3 IPv4 routing
!
ip routing

!
! Enable IPv6 routing
!
ipv6 unicast-routing

!
! VLAN definitions
!
vlan 10
 name ADMIN
 exit

vlan 20
 name SALES
 exit

vlan 30
 name IT
 exit

!
! Administration gateway
!
interface vlan 10
 description Administration-Gateway
 ip address 10.10.10.1 255.255.255.0
 ipv6 address 2001:db8:acad:10::1/64
 no shutdown
 exit

!
! Sales gateway
!
interface vlan 20
 description Sales-Gateway
 ip address 10.10.20.1 255.255.255.0
 ipv6 address 2001:db8:acad:20::1/64
 no shutdown
 exit

!
! IT gateway
!
interface vlan 30
 description IT-Gateway
 ip address 10.10.30.1 255.255.255.0
 ipv6 address 2001:db8:acad:30::1/64
 no shutdown
 exit

!
! Routed connection to R1
!
interface gigabitEthernet 1/0/1
 description Connection-to-R1
 no switchport
 ip address 10.10.254.2 255.255.255.252
 no shutdown
 exit

!
! Connection to Administration switch
!
interface gigabitEthernet 1/0/2
 description Connection-to-SW-A
 switchport mode access
 switchport access vlan 10
 no shutdown
 exit

!
! Connection to Sales switch
!
interface gigabitEthernet 1/0/3
 description Connection-to-SW-B
 switchport mode access
 switchport access vlan 20
 no shutdown
 exit

!
! Connection to IT switch
!
interface gigabitEthernet 1/0/4
 description Connection-to-SW-C
 switchport mode access
 switchport access vlan 30
 no shutdown
 exit

!
! Save configuration
!
end
copy running-config startup-config
```

---

# Configurations-SW-A

```text
enable
configure terminal

!
! Basic identification
!
hostname SW-A
no ip domain-lookup

!
! Administration VLAN
!
vlan 10
 name ADMIN
 exit

!
! Administration PCs
!
interface range fastEthernet 0/1-3
 description Administration-PCs
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
 no shutdown
 exit

!
! Uplink to CORE-SW1
!
interface gigabitEthernet 0/1
 description Uplink-to-CORE-SW1
 switchport mode access
 switchport access vlan 10
 no shutdown
 exit

!
! Save configuration
!
end
copy running-config startup-config
```

---

# Configurations-SW-B

```text
enable
configure terminal

!
! Basic identification
!
hostname SW-B
no ip domain-lookup

!
! Sales VLAN
!
vlan 20
 name SALES
 exit

!
! Sales PCs
!
interface range fastEthernet 0/1-3
 description Sales-PCs
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
 no shutdown
 exit

!
! Uplink to CORE-SW1
!
interface gigabitEthernet 0/1
 description Uplink-to-CORE-SW1
 switchport mode access
 switchport access vlan 20
 no shutdown
 exit

!
! Save configuration
!
end
copy running-config startup-config
```

---

# Configurations-SW-C

```text
enable
configure terminal

!
! Basic identification
!
hostname SW-C
no ip domain-lookup

!
! IT VLAN
!
vlan 30
 name IT
 exit

!
! IT PCs
!
interface range fastEthernet 0/1-3
 description IT-PCs
 switchport mode access
 switchport access vlan 30
 spanning-tree portfast
 no shutdown
 exit

!
! Uplink to CORE-SW1
!
interface gigabitEthernet 0/1
 description Uplink-to-CORE-SW1
 switchport mode access
 switchport access vlan 30
 no shutdown
 exit

!
! Save configuration
!
end
copy running-config startup-config
```
