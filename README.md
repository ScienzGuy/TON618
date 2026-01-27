# TON618: The Standalone Network Black Hole 🕳️

Named after the ultramassive black hole, **TON618** is a production-grade, standalone Raspberry Pi network appliance. It serves as the primary security sentry for the entire local network, pulling every ad, tracker, and malicious domain into an unescapable singularity.

## 🚀 Sovereign Architecture
Unlike standard forwarding setups, TON618 operates as a **Sovereign Recursive DNS Resolver**. It communicates directly with the Internet’s Root Nameservers, eliminating reliance on third-party providers (Cloudflare/Google) for maximum privacy.

### Core Specifications
* **Hardware:** Raspberry Pi 4 Model B (4GB RAM) | High-Endurance MicroSD.
* **Blocklist Density:** 1,123,000+ unique domains (Primary: **HaGeZi Ultimate**).
* **DNS Resolution:** Local Recursive (**Unbound**) | DNSSEC Validated.
* **VPN Protocol:** **WireGuard** (UDP 51820) via PiVPN.
* **Security Layer:** **UFW** (Uncomplicated Firewall) | Hardened ingress/egress policies.
* **Monitoring:** **Glances** Standalone System Telemetry.

## ⚙️ The Hardened Stack: Deep-Dive

### 1. Recursive Resolution (Unbound)
TON618 acts as its own Root Authority, ensuring absolute DNS privacy.
* **Optimization:** Runs on port 5335 with local loopback hardening.
* **Security:** Cryptographically verifies all responses using DNSSEC to prevent spoofing.

### 2. The VPN-to-WAN Bridge (NAT Masquerade)
To provide seamless mobile protection on 5G/cellular, a manual NAT masquerade bridges the virtual VPN subnet to the physical Ethernet interface. 

> **Commands used:**
> sudo iptables -t nat -A POSTROUTING -s 10.228.35.0/24 -o eth0 -j MASQUERADE
> sudo netfilter-persistent save

### 3. Perimeter Defense (UFW)
Post-deployment, the system was hardened using **UFW**. Access is restricted to strictly necessary ports (DNS, WireGuard, and SSH), with explicit permissions for the local loopback interface to allow Pi-hole and Unbound to communicate securely.

## ⚖️ Challenges & Surgical Fixes
Operating at a 1.12M domain block-rate introduced unique hurdles that required high-fidelity troubleshooting.

### 1. The "Sync" Paradox (Google Ecosystem)
**Challenge:** Aggressive filtering disrupted background heartbeats for Google Workspace.
**Solution:** Whitelisted volatile-pa.googleapis.com and userlocation.googleapis.com to restore Google Keep and system-level synchronization.

### 2. The Android Auto Handshake
**Challenge:** Always-on VPN encryption interfered with wireless projection in vehicles.
**Solution:** Configured App-level Split Tunneling within the WireGuard client to exclude Android Auto and Google Play Services from the tunnel.

### 3. Kubernetes Cluster Bypass
**Challenge:** Filtering interfered with internal service discovery for the nodes in the separate **Project Jupiter** clusters.
**Solution:** Established a **Zero-Filtering Group** via Pi-hole Client Group Management, ensuring the Jupiter nodes (Io, Europa, etc.) have unfiltered resolution for critical API handshakes.

## 📊 Maintenance & Monitoring
The singularity is maintained via a custom administrative suite for 24/7 stability:

* **Telemetry:** Glances provides real-time monitoring of CPU thermals and I/O wait times.
* **Command Suite:**
    * ton618-update: Unified OS, Pi-hole, and Gravity sync.
    * ton618-cache: Real-time Unbound recursive statistics.
    * ton618-health: Systemd status check for the security stack (Pi-hole, Unbound, WireGuard, UFW, Glances).

## 👨‍💻 Maintainer
**ScienzGuy** [@ScienzGuy](https://github.com/ScienzGuy)

---

## 🔗 Related Projects

| Project | Role | Tech Stack |
| :--- | :--- | :--- |
| **TON618** | Standalone Sentry (Security) | Pi-hole, Unbound, WireGuard |
| **[Pi-Ollama](https://github.com/ScienzGuy/Pi-Project-GAIa)** | GAIa Cluster (AI) | Docker, Ollama, Open WebUI |
| **[Pi-BOINC](https://github.com/ScienzGuy/Pi-Project-Theia)** | Theia Cluster (Compute) | Bare Metal Debian, BOINC |
