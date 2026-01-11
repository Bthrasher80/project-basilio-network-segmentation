🧱 Artifact 1
Network Segmentation & Lab Isolation Using pfSense and Proxmox
Project: Project Basilio — Enterprise Cyber Home Lab

📌 Objective

Design and implement a secure, enterprise-style network segmentation model for a home SOC lab that:

Prevents lab activity from impacting household devices

Enforces clear trust boundaries between networks

Allows safe vulnerability scanning and telemetry collection

Mirrors real-world SOC / enterprise infrastructure patterns

🧠 Problem Statement

Initially, the lab and home devices shared routing and broadcast domains via a consumer router. This caused:

Network instability

Broadcast and multicast noise

Risk of impacting home devices during scanning

No clear separation between management, lab, and home traffic

This design was not suitable for SOC operations, vulnerability management, or portfolio demonstration.

🛠️ Solution Overview

I implemented pfSense as an authoritative firewall/router inside a Proxmox host to enforce strict network segmentation across three zones:

Zone	Subnet	Purpose
Home Network	192.168.1.0/24	Household Wi-Fi & personal devices
Management Plane	192.168.50.0/24	Proxmox host & infrastructure
SOC Lab Network	10.0.0.0/24	Lab endpoints, scanners, SOC tools

All routing between zones is explicitly controlled by pfSense.

🧱 Final Network Architecture
Internet
   |
Verizon G3100 (Home Network: 192.168.1.0/24)
   |
USB Ethernet → Proxmox Host (vmbr0)
   |
pfSense WAN (192.168.50.0/24)
pfSense LAN (10.0.0.0/24)
   |
Physical Switch
   |
SOC Lab Endpoints (10.0.0.x)

⚙️ Key Technical Details
Proxmox Networking

vmbr0 → WAN / Management bridge

vmbr1 → LAN / SOC Lab bridge

No bridging between vmbr0 and vmbr1 (prevents flat network)

pfSense Configuration

WAN: 192.168.50.x

LAN: 10.0.0.1/24

DHCP on LAN: 10.0.0.50 – 10.0.0.200

Outbound NAT: Automatic

DNS Resolver enabled

Firewall Behavior

Home → Lab: Blocked

Lab → Management: Blocked

Lab → Internet: Allowed via NAT

Management access restricted to authorized paths only

✅ Validation & Testing

Lab Endpoint Validation

Received DHCP lease from pfSense (10.0.0.x)

Successful internet access

No access to home network devices

Isolation Validation

Home Wi-Fi devices cannot access lab systems

Lab systems cannot access Proxmox management plane

Proxmox remains accessible locally on host

Operational Validation

pfSense enforced NAT and firewall policies

Temporary connectivity loss during cutover resolved normally

Final state stable and reproducible

🔐 Security & SOC Relevance

This architecture enables:

Safe vulnerability scanning (Qualys / OpenVAS)

Controlled attack simulation

Clean telemetry ingestion (Wazuh / SIEM)

Realistic SOC access models (jump host / VPN)

Enterprise-style trust boundaries

This is the minimum viable architecture for serious SOC and vulnerability-management work.

📸 Evidence Captured

Proxmox network bridges (vmbr0, vmbr1)

pfSense WAN/LAN configuration

DHCP lease assignment

Endpoint ipconfig showing pfSense routing

Proxmox host IP verification

Firewall-enforced access restrictions

(Screenshots archived locally and available on request.)

🧠 Lessons Learned

Flat networks do not scale for security work

Consumer routers are insufficient for SOC labs

Firewall enforcement must be intentional, not accidental

Losing “easy access” is often a sign of correct design

🚀 Next Steps

Secure remote access via WireGuard VPN

Vulnerability scanning (Qualys VMDR / OpenVAS)

Centralized logging (Wazuh / SIEM)

Detection engineering and response simulations

## Evidence

Screenshots in the `/screenshots` directory demonstrate:

- Proxmox Linux bridge configuration enforcing L2 separation
- pfSense operating as the authoritative router/firewall
- Distinct WAN (home) and LAN (lab) subnets
- Stable routing and DHCP for lab-only devices

🏁 Status

Artifact 1: COMPLETE ✅
Network segmentation is stable, enforced, and production-ready for lab use.
