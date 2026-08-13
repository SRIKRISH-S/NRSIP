# Network Routing & Simulation Lab Experiments (NRSIP)

[![Cisco Packet Tracer](https://img.shields.io/badge/Cisco%20Packet%20Tracer-v8.0+-005073?style=for-the-badge&logo=cisco&logoColor=white)](https://www.netacad.com/courses/packet-tracer)
[![Networking](https://img.shields.io/badge/Domain-Computer%20Networking-blue?style=for-the-badge&logo=cisco)](https://www.cisco.com)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](LICENSE)

A collection of **Cisco Packet Tracer (.pkt)** lab topology files and configurations for **Network Routing and Protocols (NRSIP)**. This repository provides complete setups for Virtual Local Area Networks (VLANs), Inter-VLAN Routing (Router-on-a-Stick), Trunking protocols, and core networking lab implementations.

---

## 📋 Table of Contents
- [Overview](#-overview)
- [Repository Structure](#-repository-structure)
- [Featured Experiment: Inter-VLAN Routing](#-featured-experiment-inter-vlan-routing)
  - [Topology Architecture](#topology-architecture)
  - [Addressing Table](#addressing-table)
  - [Switch Configuration (Cisco 2960)](#switch-configuration-cisco-2960)
  - [Router Configuration (Cisco 1941)](#router-configuration-cisco-1941)
  - [Verification & Connectivity Testing](#verification--connectivity-testing)
- [How to Run Experiments](#-how-to-run-experiments)
- [Prerequisites](#-prerequisites)

---

## 🌐 Overview

This repository hosts interactive simulation topologies created for network simulation labs. The topologies demonstrate key networking concepts such as:
- **L2 Switching**: VLAN creation, access port assignment, and 802.1Q IEEE trunking.
- **L3 Routing**: Router-on-a-Stick (ROAS) inter-VLAN communication using logical sub-interfaces.
- **Packet Verification**: End-to-end ping testing and ICMP traffic analysis.

---

## 📁 Repository Structure

| Lab File | Description | Technologies / Concepts |
| :--- | :--- | :--- |
| **`Experiment 1 cisco.pkt`** | Introductory network topology setup and basic device connectivity. | IP Addressing, Switch Basics, Ping Verification |
| **`Experiment 2 cisco.pkt`** | Multi-device network configuration and initial routing setups. | Subnetting, Default Gateways, Static Routes |
| **`Cisco exp 3.pkt`** | Advanced VLAN segmentation and access control configuration. | VLAN 10 (Sales), VLAN 20 (HR), Access Trunk Ports |
| **`Cisco exp 4.pkt`** | Inter-VLAN Routing utilizing Router-on-a-Stick architecture. | 802.1Q Encapsulation, Sub-interfaces, Gateways |

---

## 🛠️ Featured Experiment: Inter-VLAN Routing & VLAN Configuration

### Aim
To configure **Virtual Local Area Networks (VLANs)** on a Cisco switch and implement **Inter-VLAN Routing** using a Cisco router so hosts in different subnets/VLANs can communicate securely and efficiently.

---

### Topology Architecture

```
                      +-------------------+
                      |   Cisco Router    |
                      |      (1941)       |
                      +---------+---------+
                                |  GigabitEthernet0/0 (Trunk: 802.1Q)
                                |
                      +---------+---------+
                      |   Cisco Switch    |
                      |      (2960)       |
                      +--+---+-------+--+-+
                         |   |       |  |
         Fa0/1 ----------+   |       |  +---------- Fa0/4
        (VLAN 10)            |       |            (VLAN 20)
                             |       |
             Fa0/2 ----------+       +---------- Fa0/3
            (VLAN 10)                        (VLAN 20)
                |                                |
         +------+------+                  +------+------+
         |     PC0     |                  |     PC2     |
         | (SALES Dept)|                  |  (HR Dept)  |
         +-------------+                  +-------------+
         |     PC1     |                  |     PC3     |
         | (SALES Dept)|                  |  (HR Dept)  |
         +-------------+                  +-------------+
```

---

### Addressing Table

| Device | Interface | IP Address | Subnet Mask | Default Gateway | VLAN | Department |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **PC0** | Fa0 | `192.168.10.2` | `255.255.255.0` | `192.168.10.1` | VLAN 10 | SALES |
| **PC1** | Fa0 | `192.168.10.3` | `255.255.255.0` | `192.168.10.1` | VLAN 10 | SALES |
| **PC2** | Fa0 | `192.168.20.2` | `255.255.255.0` | `192.168.20.1` | VLAN 20 | HR |
| **PC3** | Fa0 | `192.168.20.3` | `255.255.255.0` | `192.168.20.1` | VLAN 20 | HR |
| **Router0** | Gi0/0.10 | `192.168.10.1` | `255.255.255.0` | N/A | VLAN 10 | Gateway (Sales) |
| **Router0** | Gi0/0.20 | `192.168.20.1` | `255.255.255.0` | N/A | VLAN 20 | Gateway (HR) |

---

### Switch Configuration (Cisco 2960)

```cisco
enable
configure terminal

! 1. Create VLANs
vlan 10
 name SALES
exit

vlan 20
 name HR
exit

! 2. Assign Switch Ports to VLAN 10 (Sales)
interface range fa0/1-2
 switchport mode access
 switchport access vlan 10
exit

! 3. Assign Switch Ports to VLAN 20 (HR)
interface range fa0/3-4
 switchport mode access
 switchport access vlan 20
exit

! 4. Configure Trunk Port to Router
interface fa0/24
 switchport mode trunk
exit
end
```

---

### Router Configuration (Cisco 1941)

```cisco
enable
configure terminal

! 1. Enable Physical Interface
interface gigabitEthernet0/0
 no shutdown
exit

! 2. Configure Sub-interface for VLAN 10 (Sales Gateway)
interface gigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
exit

! 3. Configure Sub-interface for VLAN 20 (HR Gateway)
interface gigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
exit

end
```

---

### Verification & Connectivity Testing

Execute the following commands in Cisco Packet Tracer CLI to verify configuration:

#### 1. Verify VLAN Creation on Switch
```cisco
Switch# show vlan brief
```

#### 2. Verify Trunking Port Status
```cisco
Switch# show interfaces trunk
```

#### 3. Verify Router Sub-interfaces
```cisco
Router# show ip interface brief
```

#### 4. Test Connectivity (Ping Verification)
From **PC0** (`192.168.10.2`):
- **Same VLAN Communication**: `ping 192.168.10.3` (PC1) $\rightarrow$ **Success**
- **Default Gateway Ping**: `ping 192.168.10.1` (Router Gi0/0.10) $\rightarrow$ **Success**
- **Inter-VLAN Communication**: `ping 192.168.20.2` (PC2 in VLAN 20) $\rightarrow$ **Success**

---

## 🚀 How to Run Experiments

1. Download and install **[Cisco Packet Tracer](https://www.netacad.com/courses/packet-tracer)** (v8.0 or higher recommended).
2. Clone this repository:
   ```bash
   git clone https://github.com/SRIKRISH-S/NRSIP.git
   ```
3. Open any `.pkt` file directly with Cisco Packet Tracer.
4. Interact with the devices via the **CLI** or use the **Simulation Mode** to visually inspect ICMP/PDU packet propagation across VLANs.

---

## ⚡ Prerequisites

- **Cisco Packet Tracer v8.x**
- Basic understanding of **OSI Layer 2 / Layer 3 protocols**, **IP Subnetting**, and **Cisco IOS CLI commands**.

---

*Maintained by [SRIKRISH-S](https://github.com/SRIKRISH-S)*
