# 🛡️ NetGuard Lab
### Network Segmentation, Stateful Firewall & 3D Security Visualization

![Status](https://img.shields.io/badge/Status-Completed-success)
![Platform](https://img.shields.io/badge/Platform-GNS3%20%7C%20VMware-blue)
![Firewall](https://img.shields.io/badge/Firewall-nftables-red)
![OS](https://img.shields.io/badge/OS-Debian%20%7C%20Ubuntu-orange)
![Visualization](https://img.shields.io/badge/Visualization-Blender-purple)

**NetGuard Lab** is a virtual network security project designed to demonstrate network segmentation, Linux routing, stateful firewall filtering, and network traffic analysis.

The lab was built using GNS3, VMware Workstation, Debian Linux, Ubuntu Server, and nftables.

A custom 3D animation created in Blender illustrates how network traffic is allowed or blocked according to firewall rules.

---

## 📌 Project Overview

The objective of NetGuard Lab is to design and secure a small enterprise-style network consisting of two separate subnets.

The project demonstrates:

- Network segmentation using IPv4 subnetting.
- Inter-network routing through a Debian Linux router.
- Stateful packet filtering using nftables.
- Controlled access to an Apache web server.
- Blocking unauthorized traffic.
- Firewall logging and security verification.
- 3D visualization of network traffic.

## 🏗️ Network Architecture

The infrastructure consists of two networks connected through a Debian router/firewall.

```text
                NETGUARD LAB
             Network Architecture

    LAN1 — EMPLOYEES
      192.168.10.0/24

    PC1          PC2         Windows Host
192.168.10.10 192.168.10.11 192.168.10.254
      |           |              |
      +-----------+--------------+
                  |
          GNS3 / VMware VMnet2
                  |
         Debian Router / Firewall
           192.168.10.1
           192.168.20.1
                  |
          GNS3 / VMware VMnet1
                  |
      +-----------+-----------+
      |                       |
 Ubuntu Server               PC3
 192.168.20.10          192.168.20.20
 Apache HTTP
      |
    LAN2 — SERVERS
    192.168.20.0/24
```

The Debian router connects both networks and applies firewall rules to forwarded traffic.

## 🌐 IP Addressing

| Device | Interface / Network | IP Address | Role |
|---|---|---|---|
| PC1 | LAN1 | 192.168.10.10/24 | Employee workstation |
| PC2 | LAN1 | 192.168.10.11/24 | Employee workstation |
| Windows Host | VMnet2 | 192.168.10.254/24 | Testing workstation |
| Debian Router | ens33 | 192.168.10.1/24 | LAN1 gateway |
| Debian Router | ens37 | 192.168.20.1/24 | LAN2 gateway |
| Ubuntu Server | ens37 | 192.168.20.10/24 | Apache web server |
| PC3 | LAN2 | 192.168.20.20/24 | Server-network workstation |

**Additional management interface:** Ubuntu Server uses a separate NAT-connected interface for management.

## 🧰 Technologies Used

| Technology | Purpose |
|---|---|
| GNS3 | Network topology and simulation |
| VMware Workstation | Virtual machine infrastructure |
| Debian Linux | Routing and firewall |
| Ubuntu Server | Web server hosting |
| nftables | Stateful firewall filtering |
| Apache HTTP Server | HTTP service |
| VPCS | Virtual network clients |
| Blender | Cinematic 3D network visualization |

## 🔐 Firewall Configuration

NetGuard Lab uses a stateful firewall based on **nftables**.

The firewall controls traffic forwarded between LAN1 and LAN2.

### Security Policy

| Traffic | Source | Destination | Decision |
|---|---|---|---|
| HTTP (TCP/80) | LAN1 | Ubuntu Server | ✅ ALLOW |
| ICMP Echo Request | LAN1 | Ubuntu Server | ❌ DROP |
| SSH (TCP/22) | LAN1 | Ubuntu Server | ❌ DROP |
| Established/Related | Existing connections | Return traffic | ✅ ALLOW |
| Other forwarded traffic | Any | Any | ❌ DROP |

### Example nftables Rules

```nft
table inet netguard {
    chain forward {
        type filter hook forward priority 0;
        policy drop;

        ct state established,related accept

        ip saddr 192.168.10.0/24 \
        ip daddr 192.168.20.10 \
        tcp dport 80 \
        ct state new accept

        limit rate 5/minute burst 5 packets \
        log prefix "NETGUARD_DROP : " drop
    }
}
```

**Important:** These rules filter forwarded traffic. They do not define equivalent restrictions for traffic destined to the firewall itself.

## 🧪 Security Testing

The firewall policy was validated through connectivity and service tests.

### Test 1 — HTTP Access

**Objective:** Verify that authorized HTTP traffic reaches the Ubuntu web server.

```bash
curl -I http://192.168.20.10
```

**Result:**

```text
HTTP/1.1 200 OK
```

**Status:** ✅ PASSED

### Test 2 — ICMP Filtering

**Objective:** Verify that ping traffic from LAN1 to the Ubuntu Server is blocked.

```bash
ping 192.168.20.10
```

**Result:** ICMP requests are blocked by the firewall.

**Status:** ✅ PASSED

### Test 3 — SSH Filtering

**Objective:** Verify that unauthorized SSH connections from LAN1 to the Ubuntu Server are blocked.

**Destination:** `192.168.20.10:22`

**Result:** TCP/22 traffic is blocked by the forwarding policy.

**Status:** ✅ PASSED

### Test 4 — Firewall Logging

Dropped packets are logged using the prefix:

```text
NETGUARD_DROP :
```

Logs can be inspected on the Debian router using:

```bash
sudo journalctl -k | grep NETGUARD_DROP
```

**Status:** ✅ PASSED

## 🎬 3D Network Visualization

A custom cinematic animation was developed using **Blender and Python scripting**.

The visualization demonstrates:

- The segmented network architecture.
- HTTP requests passing through the firewall.
- Authorized communication with the Ubuntu Server.
- ICMP traffic being blocked.
- SSH traffic being blocked.
- Reactive firewall effects and security logs.

**Animation specifications:**

| Property | Value |
|---|---|
| Software | Blender |
| Resolution | 1920 × 1080 |
| Frame Rate | 24 FPS |
| Duration | 30 seconds |
| Style | Isometric 3D / Cybersecurity |

The animation is a visual explanation of the lab's tested security policy, not a live packet capture.

## 📂 Repository Structure

```text
NetGuard-Lab/
│
├── README.md
├── LICENSE
├── .gitignore
│
├── docs/
│   ├── architecture.md
│   ├── installation.md
│   └── security-testing.md
│
├── configs/
│   └── nftables.conf
│
├── screenshots/
│   ├── topology.png
│   └── firewall-demo.png
│
└── animation/
    └── README.md
```

Supporting files and screenshots can be added as the documentation is finalized.

## 🚀 Future Improvements

Potential future extensions include:

- IDS/IPS integration using Suricata.
- Centralized security logging.
- Network monitoring dashboards.
- VLAN-based segmentation.
- Additional firewall policies.
- Automated security testing.

## 🎯 Learning Outcomes

Through this project, I gained practical experience with:

- Virtual network design.
- IPv4 addressing and routing.
- Linux network administration.
- Stateful firewall configuration.
- Network security testing.
- Troubleshooting virtualized environments.
- Technical documentation.
- Python-based 3D visualization.

---

### ⭐ NetGuard Lab — Building, Securing & Visualizing Networks

*A hands-on networking and cybersecurity project.*
