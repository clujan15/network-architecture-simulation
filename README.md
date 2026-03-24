# 🔐 Secure Enterprise Network Security Configuration

A simulated enterprise-level network security project built in **Cisco Packet Tracer**, demonstrating wireless security, VLAN segmentation, inter-VLAN routing, access control, and real-time troubleshooting techniques.

---

## 📋 Project Overview

This project simulates a real-world enterprise network environment with 5+ interconnected devices. It covers end-to-end network security configuration including WPA2 encryption, MAC address filtering, DHCP/DNS, firewall rules, VLAN segmentation, and ACL-based traffic enforcement.

---

## 🛠️ Tools & Technologies

| Tool | Purpose |
|---|---|
| Cisco Packet Tracer | Network simulation environment |
| Cisco 2911 Router | Inter-VLAN routing, DHCP, ACLs |
| Cisco 2960 Switch | VLAN configuration, trunk ports |
| WRT300N Wireless AP | WPA2, MAC filtering, firewall |
| PCs / IoT Endpoints | End devices across VLANs |

---

## 🗂️ Project Structure

```
enterprise-network-security/
│
├── README.md
├── topology.pkt                  ← Cisco Packet Tracer project file
│
├── screenshots/
│   ├── 01_topology.png           ← Full network topology
│   ├── 02_wpa2_config.png        ← WPA2-Personal + AES setup
│   ├── 03_mac_filter.png         ← MAC address filtering
│   ├── 04_dhcp_pool.png          ← DHCP server configuration
│   ├── 05_dhcp_ip_received.png   ← PC receiving dynamic IP
│   ├── 06_vlan_creation.png      ← VLAN 10/20/30 CLI setup
│   ├── 07_intervlan_routing.png  ← Router sub-interfaces
│   ├── 08_acl_config.png         ← ACL rules on router
│   ├── 09_ping_success.png       ← Successful ping between VLANs
│   ├── 10_ping_blocked.png       ← Guest → Employee traffic blocked
│   ├── 11_traceroute.png         ← Traceroute output
│   └── 12_firewall.png           ← SPI Firewall enabled
│
└── configs/
    ├── router_config.txt         ← Full router running-config
    └── switch_config.txt         ← Full switch running-config
```

---

## 🏗️ Network Topology

The network consists of 5+ devices across 3 VLANs connected through a central router and managed switch:

```
[IoT Device]   [Laptop]          [PC - Guest]
     |              |                  |
     └──────────────┘                  |
       [Wireless AP]              [Switch Port 2]
            |                         |
     [Switch Port 24 - Trunk]   [Switch Port 1]
                   \               /
                    [Cisco Switch]
                          |
                    [Cisco Router]
                    /     |     \
               VLAN10  VLAN20  VLAN30
             Employee  Guest  Management
```

---

## 🔒 Part 1 — Wireless Security (WPA2 + MAC Filtering)

**Objective:** Restrict wireless access to authorized devices only.

**Configuration:**
- SSID: `EnterpriseNet`
- Security Mode: **WPA2-Personal**
- Encryption: **AES**
- MAC Address Filter: **Permit-only list** of approved device MACs

**Result:** 100% of simulated unauthorized connection attempts were blocked.

📸 See: `screenshots/02_wpa2_config.png`, `screenshots/03_mac_filter.png`

---

## 🌐 Part 2 — DHCP Server & DNS Resolution

**Objective:** Automate dynamic IP assignment across the network.

**DHCP Pool Configuration (Router):**

| Parameter | Value |
|---|---|
| Pool Name | EmployeePool |
| Network | 192.168.10.0/24 |
| Default Gateway | 192.168.10.1 |
| DNS Server | 8.8.8.8 |
| Start IP | 192.168.10.10 |
| Max Users | 50 |

All PCs were set to DHCP mode and successfully received dynamic IP addresses.

📸 See: `screenshots/04_dhcp_pool.png`, `screenshots/05_dhcp_ip_received.png`

---

## 📡 Part 3 — VLAN Segmentation

**Objective:** Isolate traffic across 3 organizational segments on 5 subnets.

| VLAN ID | Name | Subnet |
|---|---|---|
| VLAN 10 | Employee | 192.168.10.0/24 |
| VLAN 20 | Guest | 192.168.20.0/24 |
| VLAN 30 | Management | 192.168.30.0/24 |

**Switch CLI Configuration:**

```bash
vlan 10
 name Employee
vlan 20
 name Guest
vlan 30
 name Management

interface fastethernet 0/1
 switchport mode access
 switchport access vlan 10

interface fastethernet 0/24
 switchport mode trunk
```

📸 See: `screenshots/06_vlan_creation.png`

---

## 🔀 Part 4 — Inter-VLAN Routing (Router-on-a-Stick)

**Objective:** Enable controlled communication between VLANs via sub-interfaces.

**Router Sub-Interface Configuration:**

```bash
interface gigabitethernet 0/0.10
 encapsulation dot1q 10
 ip address 192.168.10.1 255.255.255.0

interface gigabitethernet 0/0.20
 encapsulation dot1q 20
 ip address 192.168.20.1 255.255.255.0

interface gigabitethernet 0/0.30
 encapsulation dot1q 30
 ip address 192.168.30.1 255.255.255.0
```

📸 See: `screenshots/07_intervlan_routing.png`

---

## 🚧 Part 5 — Access Control Lists (ACLs)

**Objective:** Enforce security policy preventing Guest users from accessing Employee resources.

**ACL Rule:**

```bash
access-list 100 deny ip 192.168.20.0 0.0.0.255 192.168.10.0 0.0.0.255
access-list 100 permit ip any any

interface gigabitethernet 0/0.20
 ip access-group 100 in
```

**Result:** Guest VLAN (20) traffic to Employee VLAN (10) is completely blocked. All other traffic flows normally.

📸 See: `screenshots/08_acl_config.png`

---

## 🧪 Part 6 — Troubleshooting & Diagnostics

**Objective:** Verify configuration correctness and simulate real-time network troubleshooting.

**Tests Performed:**

| Test | Source | Destination | Result |
|---|---|---|---|
| Ping gateway | Employee PC | 192.168.10.1 | ✅ Success |
| Ping cross-VLAN | Employee PC | 192.168.20.1 | ✅ Success |
| Ping blocked route | Guest PC | 192.168.10.10 | ❌ Blocked (ACL) |
| Traceroute | Employee PC | 192.168.30.1 | ✅ Path traced |

📸 See: `screenshots/09_ping_success.png`, `screenshots/10_ping_blocked.png`, `screenshots/11_traceroute.png`

---

## 🔥 Part 7 — Firewall Configuration

**Objective:** Add stateful packet inspection at the wireless access point level.

- Enabled **SPI (Stateful Packet Inspection) Firewall**
- Configured to block unsolicited inbound traffic
- Telnet (port 23) access restricted

📸 See: `screenshots/12_firewall.png`

---

## ✅ Key Outcomes

- Designed a multi-device enterprise topology with layered security
- Blocked 100% of simulated unauthorized wireless access attempts via WPA2 + MAC filtering
- Segmented network traffic across 3 VLANs with full inter-VLAN routing
- Enforced security policies using ACLs (Guest → Employee access denied)
- Validated the network using ping, traceroute, and packet capture diagnostics
- Simulated real-world enterprise troubleshooting workflows

---

## 🚀 How to Run This Project

1. Download and install **[Cisco Packet Tracer](https://www.netacad.com/courses/packet-tracer)** (free with NetAcad account)
2. Clone this repository
3. Open `topology.pkt` in Packet Tracer
4. Explore device configurations via the CLI or Config tabs
5. Run ping/traceroute tests from any PC's Desktop > Command Prompt

---

## 📄 License

This project is for educational purposes. Built as part of a network security coursework final exam.
