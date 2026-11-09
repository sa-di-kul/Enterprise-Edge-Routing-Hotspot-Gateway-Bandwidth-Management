# Enterprise Edge Routing, Hotspot Gateway & Bandwidth Management Architecture

This project provides comprehensive instructions for deploying an enterprise edge router using **MikroTik RouterOS** and **WinBox**. It features an ISP WAN connection, a secure wired LAN, an isolated Wireless Hotspot Captive Portal, and per-user bandwidth control (speed limiting).

---

## 📌 Project Specifications & IP Scheme

* **Router Management Tool:** WinBox
* **WAN Interface (`ether1`):** ISP Connection (DHCP Client / NAT Enabled)
* **LAN Subnet (`ether2`–`ether5`):** `192.168.10.1/24` (Bridged Local Network)
* **Hotspot Subnet (`wlan1`):** `192.168.20.1/24` (Captive Portal Network)
* **DHCP Address Pool (Hotspot):** `192.168.20.10` – `192.168.20.254`
* **Captive Portal Domain:** `login.net`
* **Bandwidth Profiles:** 5 Mbps Upload/Download, 10 Mbps Upload/Download

---

## 🚀 Step-by-Step Configuration Guide

### Step 1: WAN Interface & Internet Connectivity
1. Open **WinBox** and connect to your MikroTik router.
2. Go to **IP** -> **DHCP Client**, click **+**, set `Interface: ether1`, check **Use Peer DNS**, and click **OK**.
3. Go to **IP** -> **Firewall** -> **NAT** tab, click **+**:
   * **General:** `Chain: srcnat`, `Out. Interface: ether1`
   * **Action:** `Action: masquerade`
4. Verify connectivity by running `ping google.com` in **New Terminal**.

### Step 2: Wired LAN Bridge & IP Address Setup
1. Go to **Bridge** -> **Bridge** tab -> Click **+** -> Name it `bridge-lan`.
2. Go to **Ports** tab -> Add `ether2`, `ether3`, `ether4`, and `ether5` to `bridge-lan`.
3. Go to **IP** -> **Addresses** -> Click **+**:
   * `Address`: `192.168.10.1/24`
   * `Interface`: `bridge-lan`
4. Go to **IP** -> **DHCP Server** -> Click **DHCP Setup** -> Select `bridge-lan` interface and complete the wizard.

### Step 3: Wireless Hotspot Server Setup
1. Go to **Wireless** -> Select `wlan1` -> Set `Mode: ap-bridge`, `SSID: Free-WiFi`, and click **Enable**.
2. Go to **IP** -> **Addresses** -> Click **+**:
   * `Address`: `192.168.20.1/24`
   * `Interface`: `wlan1`
3. Go to **IP** -> **Hotspot** -> Click **Hotspot Setup**:
   * `Hotspot Interface`: `wlan1`
   * `Local Address of Network`: `192.168.20.1/24`
   * `Address Pool`: `192.168.20.10-192.168.20.254`
   * `DNS Name`: `login.net`
   * Create an initial administrative user/password when prompted.

### Step 4: Bandwidth Control & User Profiles
1. Go to **IP** -> **Hotspot** -> **User Profiles** tab -> Click **+**:
   * **Profile 1:** `Name: 5Mbps-Pack` | `Rate Limit (rx/tx): 5M/5M`
   * **Profile 2:** `Name: 10Mbps-Pack` | `Rate Limit (rx/tx): 10M/10M`
2. Go to **Users** tab -> Click **+**:
   * `Name`: `user1` | `Password`: `1234` | `Profile`: `5Mbps-Pack`
3. When `user1` logs into the Wi-Fi portal (`login.net`), their bandwidth speed will be dynamically limited to 5 Mbps.

---

## 🌟 5 Key Highlights of this Architecture

1. **Dual Network Isolation:** Separates wired corporate traffic (`192.168.10.0/24`) from wireless guest traffic (`192.168.20.0/24`) for optimal security.
2. **Automated Captive Portal:** Automatically redirects wireless users to a custom login landing page (`login.net`) prior to internet grant.
3. **Granular Bandwidth Control:** Restricts upload and download speeds per user package using native MikroTik Rate Limiting.
4. **Universal WAN Routing:** Connects seamlessly to any ISP provider using dynamic DHCP or NAT masquerading.
5. **High Scalability:** Provides a ready-to-use foundation for integrating RADIUS billing servers, external APs, and custom portal HTML designs.
