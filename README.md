# 🧱 Network Segmentation & Lab Isolation
**Project Basilio — Enterprise Cyber Home Lab**

---

## 📌 Objective

Design and implement a secure, enterprise-style network segmentation model for a home SOC lab that:

- Prevents lab activity from impacting household devices
- Enforces clear trust boundaries between networks
- Allows safe vulnerability scanning and telemetry collection
- Mirrors real-world SOC / enterprise infrastructure patterns

---

## 🧠 Problem Statement

Initially, the lab and home devices shared routing and broadcast domains via a consumer router. This caused:

- Network instability
- Broadcast and multicast noise
- Risk of impacting home devices during scanning
- No clear separation between management, lab, and home traffic

This design was not suitable for SOC operations, vulnerability management, or portfolio demonstration.

---

## 🛠️ Solution Overview

pfSense is deployed on a dedicated Dell Optiplex as a physical firewall/router, enforcing strict network segmentation across three zones:

| Zone | Subnet | Purpose |
|---|---|---|
| Home Network | 192.168.1.0/24 | Household Wi-Fi & personal devices |
| Management Plane | 192.168.50.0/24 | Proxmox host & infrastructure |
| SOC Lab Network | 10.0.0.0/24 | Lab VMs, scanners, SOC tools |

All routing between zones is explicitly controlled by pfSense.

---

## 🧱 Current Network Architecture
Internet
|
Verizon G3100 (Home Network: 192.168.1.0/24)
|
Dell Optiplex — pfSense (Dedicated Firewall)
WAN: 192.168.1.x (home-facing)
LAN: 10.0.0.1/24 (lab-facing)
|
Physical Switch
|
Proxmox Host (10.0.0.x)
├── Wazuh Server (Ubuntu)
├── Active Directory / Domain Controller (Windows Server)
├── Victim Machine 1 (Windows)
└── Victim Machine 2 (Linux)

---

## ⚙️ Key Technical Details

**pfSense (Dedicated Hardware)**
- Deployed on Dell Optiplex — dedicated firewall, not virtualized
- WAN interface: home network uplink
- LAN interface: SOC lab network (10.0.0.1/24)
- DHCP on LAN: 10.0.0.50 – 10.0.0.200
- Outbound NAT: Automatic
- DNS Resolver enabled

**Proxmox Host**
- Hypervisor running all lab VMs
- Connected to SOC lab network (10.0.0.x)
- Hosts Wazuh, AD/DC, and victim machines

**Firewall Behavior**
- Home → Lab: Blocked
- Lab → Management: Blocked
- Lab → Internet: Allowed via NAT
- Management access restricted to authorized paths only

---

## ✅ Validation & Testing

**Lab Endpoint Validation**
- VMs receive DHCP leases from pfSense (10.0.0.x)
- Successful internet access from lab endpoints
- No access to home network devices confirmed

**Isolation Validation**
- Home Wi-Fi devices cannot reach lab systems
- Lab systems isolated from home network
- Proxmox management plane accessible only from authorized hosts

**Operational Validation**
- pfSense enforcing NAT and firewall policies
- All four VMs running stable on Proxmox
- Wazuh receiving logs from endpoints

---

## 🔐 Security & SOC Relevance

This architecture enables:
- Safe vulnerability scanning (Tenable / Qualys)
- Controlled attack simulation between victim machines
- Clean telemetry ingestion into Wazuh SIEM
- Enterprise-style trust boundaries with dedicated firewall hardware

---

## 🧠 Lessons Learned

- Flat networks do not scale for security work
- Dedicated firewall hardware provides better performance and isolation than a virtualized firewall
- Firewall enforcement must be intentional, not accidental
- Physical separation of firewall from hypervisor mirrors enterprise architecture

---

## 📸 Evidence

Screenshots in the `/screenshots` directory demonstrate:
- Proxmox node view showing all lab VMs
- pfSense WAN/LAN configuration
- DHCP lease assignments to lab endpoints
- Wazuh server running and reachable on the lab network

---

## 🏁 Status

**Artifact 1: COMPLETE ✅**
Network segmentation is stable, enforced, and production-ready for lab use.
