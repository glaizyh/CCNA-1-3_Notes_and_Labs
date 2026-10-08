# Module 14: Network Automation

## 1. Automation Overview

### Concept & Benefits

- **Definition:** Any process that is self-driven, reducing and potentially eliminating the need for human intervention.
- **Key benefits:**
  - Continuous operation (24/7, no breaks), resulting in greater output
  - Higher consistency and product uniformity
  - Rapid data collection and analytics to guide real-time decisions
  - Reduced risk to humans in hazardous environments (e.g., firefighting, mining)
  - Adaptive operational efficiency (e.g., smart power consumption, medical diagnosis, improved auto safety)

### Smart Devices & Network Automation

- **Smart device:** Any device that alters its behavior dynamically based on environmental information or external input.
- **Programming requirement:** Smart devices require programming via network automation tools to operate effectively.

---

## 2. Data Formats

### Concept & Rules

- **Purpose:** Standardized methods to store, structure, and interchange data between systems, scripts, or applications.
- **Components:**

| Component | Description |
| --- | --- |
| **Syntax** | Bracket conventions (`[ ]`, `( )`, `{ }`), white space, indentation, quotes, commas |
| **Object representation** | Characters, strings, numbers, Booleans, lists, arrays |
| **Key/value pairs** | Identifier key on the left describing the data; assigned value on the right |

### JSON (JavaScript Object Notation)

- Human-readable; widely used by web services and APIs; easy to parse in modern languages (e.g., Python).
- **Syntax rules:**
  - Hierarchical structure supporting nested values
  - `{ }` hold **objects** (one or more key/value pairs)
  - `[ ]` hold **arrays** (ordered lists of values)
  - Keys must be in **double quotes** (`"key"`)
  - Colon (`:`) separates key and value; commas (`,`) separate pairs
  - White space is not significant

```json
{
  "hostname": "R1",
  "interfaces": [
    { "name": "G0/0/0", "ip": "192.168.1.1" },
    { "name": "G0/0/1", "ip": "192.168.2.1" }
  ]
}
```

### YAML (YAML Ain't Markup Language)

- Considered a **superset of JSON**; minimalist, clean structure for easy reading and writing.
- **Syntax rules:**
  - Uses **indentation** for hierarchy (no brackets or commas)
  - Key/value pairs separated by a colon, **no quotes** required
  - Hyphens (`-`) declare list elements

```yaml
hostname: R1
interfaces:
  - name: G0/0/0
    ip: 192.168.1.1
  - name: G0/0/1
    ip: 192.168.2.1
```

### XML (eXtensible Markup Language)

- Self-descriptive markup language similar to HTML, but with **no predefined tags** or document structure.
- **Syntax rules:**
  - Data enclosed between start and end tags: `<key>value</key>`
  - Lists use repeated instances of tags for each entry

```xml
<device>
  <hostname>R1</hostname>
  <interface><name>G0/0/0</name><ip>192.168.1.1</ip></interface>
  <interface><name>G0/0/1</name><ip>192.168.2.1</ip></interface>
</device>
```

### Format Comparison

| | JSON | YAML | XML |
| --- | --- | --- | --- |
| Objects / structure | `{ }` | Indentation | `<tag></tag>` |
| Lists | `[ ]` | `-` hyphens | Repeated tags |
| Quotes on keys | Required (double) | Not used | N/A |
| Typical use | Web services / APIs | Config files (e.g., Ansible) | Self-descriptive documents |

---

## 3. Application Programming Interfaces (APIs)

### API Concept & Operational Flow

- **Definition:** Software rules and specifications enabling one application to interact with and access data or services from another application.
- **API call:** The formal message sent from a requesting application to a server hosting the required data/service.
- **Analogy (restaurant):**
  - Waiter = API
  - Order = API request
  - Kitchen = server/data
  - Food returned = API response

### API Types by Availability

| Type | Description |
| --- | --- |
| **Open / Public** | Publicly accessible, no restrictions; often needs a free API key or token to manage traffic volume |
| **Internal / Private** | Restricted to internal organizational use (e.g., internal corporate databases on mobile devices) |
| **Partner** | Used between a business and authorized external partners or contractors; requires license permission |

### Types of Web Service APIs

| Characteristic | SOAP | REST | XML-RPC | JSON-RPC |
| --- | --- | --- | --- | --- |
| **Data format** | XML | JSON, XML, YAML, and others | XML | JSON |
| **First released** | 1998 | 2000 | 1998 | 2005 |
| **Primary strength** | Well-established | Flexible formatting; most widely used | Simplicity; well-established | Simplicity |

---

## 4. RESTful Web Services

### REST Architecture & Constraints

- **REST** = Representational State Transfer; works on top of **HTTP** using standard verbs (e.g., GET, POST).
- **RESTful constraints:**
  - **Client-server:** Separates front-end client interface from back-end server logic
  - **Stateless:** No client session data stored on the server between requests; state is kept on the client
  - **Cacheable:** Responses can be cached by clients to improve performance

### CRUD Operations and HTTP Methods

| HTTP Method | RESTful Operation | Function |
| --- | --- | --- |
| **POST** | Create | Creates a new resource |
| **GET** | Read | Retrieves data from a resource |
| **PUT / PATCH** | Update | Modifies or replaces an existing resource |
| **DELETE** | Delete | Removes a resource |

### URI, URN, and URL

| Term | Meaning |
| --- | --- |
| **URI** (Uniform Resource Identifier) | Overarching string of characters identifying a network resource |
| **URN** (Uniform Resource Name) | Identifies the resource namespace/name without specifying the access protocol |
| **URL** (Uniform Resource Locator) | Defines the exact network location and protocol (HTTP, HTTPS, SFTP) used to access a resource |

### Anatomy of a RESTful Request

```text
https://api.example.com/directions/v2/route?outFormat=json&key=YOUR_KEY&from=San+Jose,Ca&to=Monterey,Ca
\_________________________/\_____________/ \_________________________________________________________/
        API server            Resource                          Query
```

| Part | Description |
| --- | --- |
| **API server** | URL identifying the server answering the REST request |
| **Resources** | The explicit service or endpoint requested |
| **Query: format** | Payload output type (e.g., `outFormat=json`) |
| **Query: key** | Authentication credential token for access tracking and rate limiting |
| **Query: parameters** | Request-specific values (e.g., `from=San+Jose,Ca&to=Monterey,Ca`) |

### Tools for Testing REST APIs

- **Web browsers:** Basic GET requests directly via URIs
- **Postman:** Build, test, and send REST API requests
- **Python:** Automated scripts and custom integration of RESTful API calls

---

## 5. Configuration Management Tools

### Traditional Management vs. Automation

- **Traditional CLI:** Manual, line-by-line configuration on individual devices; error-prone and unsustainable at scale.
- **SNMP:** Great for monitoring and gathering metrics, but historically avoided for configuration due to security vulnerabilities and implementation complexity.
- **Automation:** Software tools executing specific configuration tasks automatically.
- **Orchestration:** Arranging individual automated tasks into a coordinated, multi-step process or workflow.

### Comparison of Major Tools

| Feature | Ansible | Chef | Puppet | SaltStack |
| --- | --- | --- | --- | --- |
| **Language** | Python + YAML | Ruby | Ruby | Python |
| **Architecture** | **Agentless** | Agent-based | Supports both | Supports both |
| **Management node** | Any device as controller | Chef Master | Puppet Master | Salt Master |
| **Play/file name** | **Playbook** | Cookbook | Manifest | Pillar |

---

## 6. Intent-Based Networking (IBN) & Cisco DNA Center

### IBN Overview

- Next-generation networking model that builds on **SDN**, transforming manual, hardware-centric networks into software-centric, automated environments driven by **business objectives**.

**Three core functions**

| Function | Description |
| --- | --- |
| **Translation** | Captures business intent and translates it into actionable network policies |
| **Activation** | Uses automated workflows to install policies across physical and virtual infrastructure |
| **Assurance** | Continuous verification loop using analytics and machine learning to confirm network behavior matches policy |

### Fabric: Underlay and Overlay

| Layer | Description |
| --- | --- |
| **Underlay** | Physical infrastructure (routers, switches, links, access points) responsible for basic data forwarding |
| **Overlay (fabric)** | Virtualized logical topology on top of the underlay using encapsulation protocols (e.g., IPsec, CAPWAP) to separate services and simplify management |

### Cisco DNA Center

- Centralized hardware/software **controller and analytics platform** with a "single-pane-of-glass" interface.

**Core pillars**

| Pillar | Description |
| --- | --- |
| **SD-Access** | Consistent single network fabric across wired LAN and WLAN with automated user-access segmentation |
| **SD-WAN** | Centrally manages cloud-delivered WAN connections across data centers, branches, and multi-cloud platforms |
| **Cisco DNA Assurance** | Machine learning and telemetry to troubleshoot, predict failures, and recommend fixes |
| **Cisco DNA Security** | Uses the network as a security sensor for real-time visibility and threat containment (even in encrypted traffic) |

**Main menu functional areas**

| Area | Purpose |
| --- | --- |
| **Design** | Maps network layouts, sites, buildings, and device profiles |
| **Policy** | Defines corporate intent and business policies for automated rollout |
| **Provision** | Deploys device configurations and network services automatically |
| **Assurance** | Real-time monitoring, insights, and predictive issue detection |
| **Platform** | Integrates third-party applications and tools via APIs |
