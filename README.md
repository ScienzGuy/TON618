# Pi Project: TON618 - S-Tier Network Black Hole 🕳️

Named after the most massive black hole known to science, **TON618** is a Raspberry Pi-powered network appliance designed to pull every ad, tracker, and malicious domain into an inescapable singularity. This project combines **Pi-hole** and **PiVPN (WireGuard)** to provide aggressive, surgical-grade filtering for a residential network and mobile devices.

## 🚀 Overview
TON618 serves as the primary DNS gateway for a high-performance home network (anchored by a Nest Wifi Pro mesh system). It extends this protection to mobile devices via an encrypted WireGuard tunnel, ensuring an ad-free experience even on 5G/cellular networks.

### Core Stats
* **Blocklist Density:** 1,123,000+ domains.
* **Primary List:** HaGeZi Ultimate (High-Fidelity Filtering).
* **Block Rate:** ~22.8% of total network traffic (normalized).
* **Protocol:** WireGuard (UDP 51820).

## 🛠️ Hardware Stack
* **Controller:** Raspberry Pi 4 Model B.
* **Storage:** SanDisk Max Endurance MicroSD (optimized for high-frequency database writes to `gravity.db`).
* **Network:** Gigabit Ethernet (Hardwired to primary mesh node).

## ⚙️ Surgical Configurations
This repository documents the specific "Day 2" fixes required to stabilize high-density filtering on Android and enterprise-grade mesh environments.

### 1. The VPN-to-WAN Bridge (Masquerade)
To allow VPN clients to access the internet through the Pi's Ethernet interface, a manual NAT masquerade was required:
```bash
sudo iptables -t nat -A POSTROUTING -s 10.228.35.0/24 -o eth0 -j MASQUERADE
sudo netfilter-persistent save
```
### 2. DNS Interface Tuning
Because VPN clients reside on a virtual subnet (`10.228.35.x`), Pi-hole was adjusted to allow requests from non-local origins:
* **Setting:** `Permit all origins` (Securely gated behind the Nest Wifi Pro firewall). This allows the Pi to answer DNS queries originating from the WireGuard interface.

### 3. Service Whitelisting
To prevent "over-filtering" of essential services, the following surgical whitelists are applied:
* **Financial:** `experian.com`, `intuit.com`.
* **Productivity (Google Sync):** `volatile-pa.googleapis.com`, `userlocation.googleapis.com`.
* **Infrastructure:** `verizon.com`.

## 📱 Mobile Integration
The setup utilizes **WireGuard On-Demand** (Android "Always-on") with specific App Exclusions (Split-Tunneling) to maintain system stability:
* **Excluded Apps:** Android Auto, Google Play Services (to permit Wireless Projection).
* **MTU:** Hard-coded to `1280` for maximum compatibility across disparate cellular carriers and to prevent packet fragmentation.

## 🛠️ Troubleshooting & Dev-Ops Integration

### Kubernetes (K3s) Cluster Bypass
Aggressive DNS filtering can interfere with K3s service discovery and node-to-node heartbeats. To prevent **TON618** from disrupting cluster operations, a **Zero-Filtering Group** was implemented.

#### The Problem:
Nodes in the Kubernetes cluster experienced "CrashLoopBackOff" errors and failed API handshakes due to blocked telemetry and internal service discovery domains.

#### The S-Tier Solution:
Utilized Pi-hole **Client Group Management** to isolate cluster traffic from the 1.12M+ blocklist while maintaining local DNS resolution.

1.  **Group Creation:** Established a new group named `K3s_Bypass`.
2.  **Assignment:** Added cluster node IP addresses (for other Pi projects) as clients.
3.  **Policy:** Removed clients from the `Default` group and assigned them exclusively to `K3s_Bypass`.
4.  **Result:** Cluster nodes receive unfiltered DNS resolution, while the rest of the network remains protected.

### Common Handshake Fixes
| Service | Category | Mitigation Strategy |
| :--- | :--- | :--- |
| **Google Keep** | Sync | Whitelist `volatile-pa.googleapis.com` |
| **Android Auto** | Connectivity | Exclude "Android Auto" & "Play Services" from VPN |
| **Credit Reports**| Finance | Whitelist `experian.com` (Wildcard) |
| **Nest Wifi Pro** | Mesh | Ensure `eth0` is the primary interface for `MASQUERADE` |

---
