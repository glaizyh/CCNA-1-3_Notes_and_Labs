# Module 13: Network Virtualization

## 1. Cloud Computing

### Cloud Overview & Advantages

- **Purpose:** Addresses data management issues by providing on-demand access to a shared pool of configurable computing resources.
- **Key advantages:**
  - Global access to organizational data anywhere, anytime
  - Streamlined IT operations: subscribe only to the services you need
  - Eliminates or reduces on-site IT equipment, physical plant needs, maintenance, and personnel training
  - Rapid scaling as data volume grows

### NIST Cloud Service Models

Defined by **NIST Special Publication 800-145**.

| Model | Description |
| --- | --- |
| **SaaS** (Software as a Service) | Provider delivers access to applications and services directly over the internet |
| **PaaS** (Platform as a Service) | Provider delivers development tools, databases, and platform services used to build and host applications |
| **IaaS** (Infrastructure as a Service) | Provider gives IT managers access to virtualized networking equipment, servers, and supporting infrastructure |
| **ITaaS** (IT as a Service) | Extends cloud service models to deliver complete IT support, extending network capabilities without new infrastructure investment |

### Cloud Deployment Models

| Model | Description |
| --- | --- |
| **Public cloud** | Applications and services available to the general population |
| **Private cloud** | Infrastructure intended exclusively for a specific organization or entity (e.g., government) |
| **Hybrid cloud** | Two or more distinct clouds (e.g., part private, part public) bound together by a single architecture |
| **Community cloud** | Built for exclusive use by a specific community with customized functional or regulatory needs (e.g., healthcare compliance such as HIPAA) |

### Cloud Computing vs. Data Center

| | Data Center | Cloud Computing |
| --- | --- | --- |
| **What it is** | Physical facility, in-house or leased offsite, hosting hardware for storage and compute | Off-premise service offering rapidly provisioned virtualized resources |
| **Cost** | Expensive to build and maintain | Subscription-based |
| **Relationship** | Serves as the physical infrastructure that hosts cloud services | Runs on top of data centers |

---

## 2. Virtualization

### Concept & Advantages

- **Definition:** The foundation of cloud computing. **Separates the operating system (OS) from the physical underlying hardware.**
- **Traditional server drawbacks:** Dedicated physical hardware leads to:
  - **Server sprawl** (idle, underutilized hardware wasting power and space)
  - A **single point of failure**
- **Key benefits:**
  - Lower overall cost (less physical equipment, lower energy use, reduced rack space)
  - Faster server provisioning and higher server uptime
  - Easier prototyping, improved disaster recovery, and support for legacy operating systems

### Computer Abstraction Layers

Four standard layers:

```text
Services
   ↓
Operating System (OS)
   ↓
Firmware (ROM)
   ↓
Hardware (CPU, Memory, NIC, Disk)
```

### Hypervisors

- **Hypervisor:** A program, firmware, or software layer that creates an abstraction layer above physical hardware to create and manage **virtual machines (VMs)**.

| | Type 1 ("Bare Metal") | Type 2 ("Hosted") |
| --- | --- | --- |
| **Installed on** | Directly on physical server/networking hardware | On top of a host operating system |
| **Hardware access** | Direct access to hardware resources | Through the host OS |
| **Performance** | Higher performance, efficiency, scalability, and robustness | Lower than Type 1 |
| **Management console** | Required to manage multiple hosts | Not required (no dedicated management console software) |
| **Typical use** | Enterprise data centers and virtualization hosts | Client workstation testing and desktop environments |

---

## 3. Virtual Network Infrastructure

### Management Consoles & Resource Allocation

- **Management consoles:** Required for Type 1 hypervisors to manage multiple host servers (e.g., **Cisco UCS Manager**).
- **Hardware failure recovery:** Automatically migrates VMs to healthy physical hosts if a hardware component fails.
- **Over-allocation:** Provisioning VM memory beyond the server's total physical capacity. Viable because VMs rarely use their maximum assigned memory at the same time.

### Data Center Traffic Flows

| Traffic | Description |
| --- | --- |
| **East-West** | High-volume traffic exchanged **internally** between virtual servers/data center hosts; fluctuates continuously in location and volume |
| **North-South** | Traffic between the data center and **external** networks (internet, other cloud providers, remote data centers) |

### Virtual Network Functions

- Physical networking hardware can be segmented into virtual elements:
  - Subinterfaces
  - VLANs
  - Virtual interfaces
  - **Virtual Routing and Forwarding (VRF)**

---

## 4. Software-Defined Networking (SDN)

### Network Architecture Planes

| Plane | Role | Details |
| --- | --- | --- |
| **Control plane** | The "brains" of the device; makes forwarding decisions | Layer 2/3 protocols, topology tables, routing tables, STP, ARP tables; processed by the **CPU** |
| **Data plane** (forwarding plane) | Switch fabric connecting interfaces; forwards traffic using control plane instructions | Processed by **specialized hardware**, not the main CPU |
| **Management plane** | Used by admins to manage devices | SSH, TFTP, HTTPS, SNMP |

### Core Concept of SDN

- **SDN** = separation of the **control plane** and **data plane**. Control plane processing moves off individual devices to a **centralized controller**.

| | Traditional | SDN |
| --- | --- | --- |
| **Control plane** | Inside each individual device | Centralized in the SDN controller |
| **Data plane** | Inside each device | Remains in each device |

### SDN APIs

| API | Direction | Purpose |
| --- | --- | --- |
| **Northbound** | Controller → upstream | Communicate with applications and orchestration engines |
| **Southbound** | Controller → downstream | Define forwarding behavior on switches/routers (e.g., **OpenFlow**) |

### Virtualization Framework Technologies

| Technology | Description |
| --- | --- |
| **OpenFlow** | Standardized protocol developed at Stanford; widely used **southbound API** between controllers and forwarding devices |
| **OpenStack** | Open-source cloud orchestration platform for building scalable cloud environments and delivering **IaaS** |
| **Cisco ACI** (Application Centric Infrastructure) | Purpose-built hardware/software framework integrating cloud computing and data center management |

---

## 5. Controllers & SDN Implementations

### Flow Processing Tables

Centralized controllers populate tables in hardware/firmware to govern packet flows:

| Table | Function |
| --- | --- |
| **Flow table** | Matches incoming traffic to flow rules and executes defined actions (can operate in pipelines) |
| **Group table** | Triggers specialized actions affecting one or multiple traffic flows |
| **Meter table** | Triggers performance-based actions, such as rate-limiting flows |

### Three Main Types of SDN

| Type | Description | Example |
| --- | --- | --- |
| **Device-based SDN** | Devices are programmed directly by applications running on the device or an external server | Cisco OnePK |
| **Controller-based SDN** | Centralized controller has a topology-wide view and manipulates device traffic flows | OpenDaylight |
| **Policy-based SDN** | Adds a high-level policy layer above the controller; automated workflows and user-friendly GUIs, **no programming skills required** | Cisco APIC-EM |

### Cisco ACI Architecture

**Core components**

| Component | Description |
| --- | --- |
| **Application Network Profile (ANP)** | Collection of End-Point Groups (EPGs), connections, and policies defining application requirements |
| **APIC** (Application Policy Infrastructure Controller) | Centralized, clustered software controller that translates policy definitions into network configurations |
| **Cisco Nexus 9000 Series switches** | Application-aware hardware switches operating in the fabric |

**Spine-leaf topology**

- Two-tier architecture; leaf switches attach to spine switches.
- Provides consistent, predictable connectivity: every connected node is the same, fixed distance from every other node.

```text
        [Spine1]   [Spine2]
         / | \ \    / | \ \
        /  |  \ \  /  |  \ \
   [Leaf1] [Leaf2] [Leaf3] [Leaf4]
```

### Cisco APIC-EM & Path Trace Tool

- **APIC-EM:** Policy-based SDN controller for enterprise and campus networks; provides automated management, device inventory, and topology mapping.
- **Path Trace tool:** Graphical utility in APIC-EM that visualizes step-by-step traffic flows and evaluates **ACLs** along the path to detect **conflicting, duplicate, or shadowed entries**.
