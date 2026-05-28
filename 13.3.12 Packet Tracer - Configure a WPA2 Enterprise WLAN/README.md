# ⚙️ 13.3.12 Configure a WPA2 Enterprise WLAN on the WLC — Cisco Packet Tracer Lab

> Configure a new VLAN interface and WLAN on a WLC, secure it with WPA2-Enterprise using a RADIUS server, set up SNMP, configure a DHCP scope, and connect a wireless host.

---

## 📋 Overview

This lab builds on a previous WPA2-PSK configuration by transitioning to a WPA2-Enterprise topology suitable for larger organizations. You will create a new VLAN interface on the WLC, configure a new WLAN tied to that interface, point the WLC to an external RADIUS server for 802.1X authentication, configure SNMP trap settings, set up an internal DHCP scope for wireless clients, and finally connect a wireless host using WPA2-Enterprise credentials.

**File:** `13_3_12_Packet_Tracer_-_Configure_a_WPA2_Enterprise_WLAN.pka`  
**Platform:** Cisco Packet Tracer  
**Devices:** R-1, SW-1, WLC-1, LAP-1, Admin PC, RADIUS/SNMP Server, Wireless Host

---

## 🖧 Network Topology

### Before Configuration
![Topology Before](Topology-before.png)

### After Configuration
![Topology After](Topology-after.png)

### Addressing Table

| Device | Interface | IP Address |
|---|---|---|
| R-1 | G0/0/0.5 | 192.168.5.1/24 |
| R-1 | G0/0/0.200 | 192.168.200.1/24 |
| R-1 | G0/0/1 | 172.31.1.1/24 |
| SW1 | VLAN 200 | 192.168.200.100/24 |
| LAP-1 | G0 | DHCP |
| WLC-1 | Management | 192.168.200.254/24 |
| RADIUS/SNMP Server | NIC | 172.31.1.254/24 |
| Admin PC | NIC | 192.168.200.200/24 |

---

## 🎯 Objectives

- Configure a new VLAN interface on a WLC.
- Configure a new WLAN on a WLC.
- Configure a new scope on the WLC internal DHCP server.
- Configure the WLC with SNMP settings.
- Configure the WLC to use a RADIUS server to authenticate WLAN users.
- Secure a WLAN with WPA2-Enterprise.
- Connect hosts to the new WLC.

---

## 🛠️ Configuration Steps

### Part 1 — Create a New VLAN Interface

#### Step 1 — Open the Interfaces Page

On the **Admin PC**, browse to `https://192.168.200.254`. Navigate to **Controller > Interfaces** and click **New**.

#### Step 2 — Name the Interface and Set the VLAN ID

Fill in the new interface form:

- **Interface Name:** `WLAN-5`
- **VLAN Id:** `5`

![Create New Interface](Create-a-new-VLAN-interface.png)

Click **Apply**.

#### Step 3 — Configure the Interface Address and DHCP

On the **Interfaces > Edit** page configure:

- **VLAN Identifier:** `5`
- **IP Address:** `192.168.5.254`
- **Netmask:** `255.255.255.0`
- **Gateway:** `192.168.5.1`
- **Primary DHCP Server:** `192.168.5.1`

![Interface Edit](Create-a-new-VLAN-interface-1.png)

Click **Apply**.

---

### Part 2 — Configure a DHCP Scope

#### Step 1 — View Existing Scopes

Navigate to **Controller > Internal DHCP Server > DHCP Scope**. Two existing scopes are visible:

![DHCP Scope List](Configure-a-DHCP-Scope-2.png)

#### Step 2 — Edit the Wired Management Scope

Click **Wired Management** and configure:

- **Scope Name:** `Wired Management`
- **Pool Start Address:** `192.168.200.240`
- **Pool End Address:** `192.168.200.249`
- **Network:** `192.168.200.0`
- **Netmask:** `255.255.255.0`
- **Default Routers:** `192.168.200.1`
- **Status:** `Enabled`

![DHCP Scope Edit](Configure-a-DHCP-Scope-3.png)

Click **Apply**.

#### Step 3 — Update the Management Interface DHCP Server

Navigate to **Controller > Interfaces > management** and set:

- **Primary DHCP Server:** `192.168.100.254`

![Management Interface DHCP](Configure-a-DHCP-Scope.png)

Click **Apply**.

---

### Part 3 — Create a New WLAN

#### Step 1 — Open the WLANs Page

Navigate to **WLANs** in the top menu and click **Create New**, then **Go**.

#### Step 2 — Configure General Settings

On the **WLANs > Edit 'Floor 2 Employees'** General tab:

- **Profile Name:** `Floor 2 Employees`
- **SSID:** `SSID-5`
- **Status:** `Enabled`
- **Interface/Interface Group(G):** `management` *(will be updated to WLAN-5 after security is set)*

![Create New WLAN](Create-a-new-WLAN.png)

Click **Apply**.

---

### Part 4 — Configure SNMP

#### Step 1 — Create an SNMP Trap Receiver

Navigate to **Management > SNMP > Trap Receivers** and click **New**. Configure:

- **Community Name:** `WLAN_SNMP`
- **IP Address:** `172.31.1.254`
- **Status:** `Enable`

![SNMP New](Configure-SNMP.png)

Click **Apply**.

#### Step 2 — Verify the SNMP Trap Receiver

The SNMP Trap Receiver list now shows **WLAN_SNMP** pointing to `172.31.1.254`:

![SNMP List](Configure-SNMP-1.png)

---

### Part 5 — Configure the WLC to Use a RADIUS Server

#### Step 1 — Add the RADIUS Authentication Server

Navigate to **Security > AAA > RADIUS > Authentication** and click **New**. Configure:

- **Server Index (Priority):** `1`
- **Server IP Address:** `172.31.1.254`
- **Shared Secret:** *(enter the shared secret)*
- **Port Number:** `1812`
- **Server Status:** `Enabled`

![RADIUS New](Configure-the-WLC-to-use-a-RADIUS-server.png)

Click **Apply**.

#### Step 2 — Verify the RADIUS Server Entry

The RADIUS Authentication Servers list now shows the server at `172.31.1.254` on port `1812`:

![RADIUS List](Screenshot_2026-05-28_144613.png)

---

### Part 6 — Configure WLAN Security (WPA2-Enterprise)

#### Step 1 — Set Layer 2 Security

Navigate to **WLANs > Edit 'Floor 2 Employees' > Security > Layer 2**. Configure:

- **Layer 2 Security:** `WPA+WPA2`
- **WPA Policy:** Unchecked
- **WPA2 Policy:** `Enabled`
- **WPA2 Encryption:** `AES`
- **Authentication Key Management — 802.1X:** `Enabled`
- **PSK:** Unchecked

![WLAN Security Layer 2 - Initial](Configure-WLAN-security.png)

After applying, the Layer 2 tab confirms WPA2 Policy and AES encryption with 802.1X enabled:

![WLAN Security Layer 2 - Confirmed](Configure-WLAN-security-2.png)

#### Step 2 — Assign the RADIUS Server on the AAA Servers Tab

Navigate to the **AAA Servers** tab and set:

- **Server 1 (Authentication):** `IP:172.31.1.254, Port:1812`

![WLAN AAA Servers](Configure-WLAN-security-1.png)

Click **Apply**.

#### Step 3 — Enable FlexConnect

Navigate to the **Advanced** tab and confirm:

- **FlexConnect Local Switching:** `Enabled`
- **FlexConnect Local Auth:** `Enabled`

![WLAN Advanced](Create-a-new-WLAN-1.png)

Click **Apply**.

---

### Part 7 — Connect a Host to the Enterprise Network

#### Step 1 — Open the Wireless Network Monitor

On the **Wireless Host**, go to **Desktop > PC Wireless** and click the **Profiles** tab. Click **New** to create a new profile.

#### Step 2 — Set the SSID

Choose **Infrastructure Mode** and enter the wireless network name:

- **Wireless Network Name:** `SSID-5`

![Create Profile SSID](Configure-a-host-to-connect-to-the-enterprise-network.png)

Click **Next**.

*(Note: if the SSID field appears empty on first attempt, re-enter it.)*

![SSID Entry](Configure-a-host-to-connect-to-the-enterprise-network-1.png)

#### Step 3 — Select WPA2-Enterprise Security

On the **Wireless Security** step, select:

- **Security:** `WPA2-Enterprise`

![Wireless Security](Configure-a-host-to-connect-to-the-enterprise-network-2.png)

Click **Next**.

#### Step 4 — Confirm and Save the Profile

Review the **Confirm New Settings** screen:

| Setting | Value |
|---|---|
| Wireless Network Name | SSID-5 |
| Wireless Mode | Infrastructure |
| Security | WPA2 Enterprise |
| Authentication | Auto |
| IP Address | Auto (DHCP) |

![Confirm Settings](Configure-a-host-to-connect-to-the-enterprise-network-3.png)

Click **Save**. The profile is successfully configured:

![Profile Saved](Configure-a-host-to-connect-to-the-enterprise-network-4.png)

#### Step 5 — Connect to the Network

On the **Profiles** tab, select the **WLC NET** profile (SSID-5, WPA2-Enterprise) and click **Connect**:

![Connect Profile](Configure-a-host-to-connect-to-the-enterprise-network-5.png)

The Wireless Host will authenticate via RADIUS and receive an IP address via DHCP.

---

## 📌 Key Concepts

| Concept | Detail |
|---|---|
| **WLC** | Centrally manages multiple lightweight APs; all config done via web GUI |
| **VLAN Interface** | Maps a WLAN to a specific VLAN and subnet on the WLC |
| **WPA2-Enterprise** | Uses 802.1X + RADIUS for per-user authentication instead of a shared PSK |
| **802.1X** | IEEE standard for port-based network access control; requires a RADIUS server |
| **RADIUS** | Remote Authentication Dial-In User Service — authenticates users centrally |
| **SNMP Trap** | Unsolicited alert sent from WLC to an SNMP manager when an event occurs |
| **FlexConnect** | Allows APs to locally switch traffic and authenticate users even if WLC link fails |
| **DHCP Scope** | Pool of IP addresses the WLC's internal DHCP server distributes to wireless clients |
| **AES** | Advanced Encryption Standard — required encryption method for WPA2 |
| **Shared Secret** | Password shared between the WLC and RADIUS server to authenticate their communication |

---

## 🔍 WPA2-PSK vs WPA2-Enterprise

| Feature | WPA2-PSK (Personal) | WPA2-Enterprise |
|---|---|---|
| Authentication | Single shared passphrase | Per-user username/password via RADIUS |
| Scalability | Small networks | Large enterprise networks |
| User Management | Change password to revoke access | Disable individual user accounts |
| Activity Logging | Not per-user | Full per-user audit trail |
| Configuration Complexity | Low | Higher (requires RADIUS server) |

---

## 📁 Repository Structure

```
.
├── 13_3_12_Packet_Tracer_-_Configure_a_WPA2_Enterprise_WLAN.pka
├── README.md
├── Topology-before.png
├── Topology-after.png
├── Create-a-new-VLAN-interface.png
├── Create-a-new-VLAN-interface-1.png
├── Configure-a-DHCP-Scope.png
├── Configure-a-DHCP-Scope-2.png
├── Configure-a-DHCP-Scope-3.png
├── Create-a-new-WLAN.png
├── Create-a-new-WLAN-1.png
├── Configure-SNMP.png
├── Configure-SNMP-1.png
├── Configure-the-WLC-to-use-a-RADIUS-server.png
├── Screenshot_2026-05-28_144613.png
├── Configure-WLAN-security.png
├── Configure-WLAN-security-1.png
├── Configure-WLAN-security-2.png
├── Configure-a-host-to-connect-to-the-enterprise-network.png
├── Configure-a-host-to-connect-to-the-enterprise-network-1.png
├── Configure-a-host-to-connect-to-the-enterprise-network-2.png
├── Configure-a-host-to-connect-to-the-enterprise-network-3.png
├── Configure-a-host-to-connect-to-the-enterprise-network-4.png
└── Configure-a-host-to-connect-to-the-enterprise-network-5.png
```

---

## 🚀 Getting Started

1. Open Cisco Packet Tracer
2. Load `13_3_12_Packet_Tracer_-_Configure_a_WPA2_Enterprise_WLAN.pka`
3. Click on **Admin PC** → **Desktop** → **Web Browser**
4. Navigate to `https://192.168.200.254` and log in with `admin` credentials
5. Follow the steps above in order:
   - Create VLAN interface WLAN-5
   - Configure DHCP scope
   - Create the WLAN
   - Add SNMP trap receiver
   - Add RADIUS authentication server
   - Apply WPA2-Enterprise security to the WLAN
   - Connect the Wireless Host using the WLC NET profile
