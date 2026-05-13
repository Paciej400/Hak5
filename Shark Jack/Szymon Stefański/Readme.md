# README: Shark Jack Cable - Network Reconnaissance

## Overview

This payload transforms the **Hak5 Shark Jack Cable (SJC)** into an automated
network reconnaissance platform. Upon physical connection to a target network
via RJ45, the script autonomously performs:

1. **Network Configuration Detection** (IP, Gateway, Subnet)
2. **Host Discovery** (Alive hosts on the subnet)
3. **Deep Port Scanning** (Services and versions per host)
4. **Gateway Audit** (Detailed scan of the router/gateway)
5. **Aggressive OS Fingerprinting** (Operating system detection)
6. **Passive Traffic Analysis** (MDNS, ARP, HTTP broadcast capture)
7. **Automated Report Generation** (Timestamped loot folder)

All results are saved to a **scan_results** and **audit_results** in `/root/loot/` for later
retrieval via SSH or SCP.

> ⚠️ **DISCLAIMER:** This project was created strictly for **educational and research purposes** in a **controlled, isolated lab environment.** The techniques demonstrated here should **never** be used against systems you do not own or have explicit written permission to test. Unauthorized use of these tools is illegal and unethical.

---

## Hardware Requirements

| Component | Specification |
|---|---|
| Device | Hak5 Shark Jack Cable |
| Firmware | OpenWrt 18.06-SNAPSHOT (Hak5 v1.1.0) |
| Architecture | MIPSEL_24KC (MediaTek MT76x8) |
| Connection | RJ45 Ethernet (Primary) |
| Power | USB-C (Host-powered) |
| Management | SSH over LAN or Serial Console |

---

## Software Requirements

### Pre-installed on SJC Firmware:
- `nmap` (v7.70) - Network scanner
- `tcpdump` (v4.9.2) - Packet capture
- `bash` - Script interpreter
- `openssh` - SSH server

### Repository Note (Important):
The default Hak5 package repository
(`http://downloads.hak5.org/packages/shark/1907/`) is **no longer active**.
The OpenWrt 18.06-SNAPSHOT repositories have also been deprecated.

To restore package management functionality, update
`/etc/opkg/distfeeds.conf` with the following archive links:

```text
src/gz openwrt_base http://archive.openwrt.org/releases/18.06.9/packages/mipsel_24kc/base
src/gz openwrt_luci http://archive.openwrt.org/releases/18.06.9/packages/mipsel_24kc/luci
src/gz openwrt_packages http://archive.openwrt.org/releases/18.06.9/packages/mipsel_24kc/packages
src/gz openwrt_routing http://archive.openwrt.org/releases/18.06.9/packages/mipsel_24kc/routing
src/gz openwrt_telephony http://archive.openwrt.org/releases/18.06.9/packages/mipsel_24kc/telephony
```

Also disable signature checking in `/etc/opkg.conf`:
```text
# option check_signature
```

Then run:
```bash
opkg update
```

---

## File Structure

```
/root/
├── payload/
│   └── payload.sh              ← Main script
│
└── loot/
    └── scan_results/   ← Scan session folder
        │
        ├── SUMMARY.txt         
        ├── network_info.txt    ← SJC network configuration
        ├── alive_hosts.txt
        │── deep_scan.txt
        │── gateway_scan.txt
        ├── mdns_devices.txt    ← MDNS device identities
        ├── arp_traffic.txt     ← ARP communications map
        └── http_traffic.txt    ← Unencrypted HTTP traffic
    └── audit_results/  ← OS and service fingerprinting
        └── detailed_audit_192.168.X.X.txt
```

---

## Setup Instructions

### Step 1: Physical Setup
1. Set the switch on the Shark Jack Cable to **Arming Mode**
   ( switch to the middle ).
2. Connect the **USB-C** end to a power source
   ( laptop, phone charger, or powered USB hub ).
3. Connect the **RJ45** end to your target network router or switch.
4. Wait for the **LED to breathe green** ( device has booted successfully ).

### Step 2: Management Connection
Since USB-C networking (RNDIS) may be unreliable on Windows/Linux,
the recommended management method is **SSH over RJ45**.
>Note that serial console is most usable for debugging!

#### Find the SJC IP Address:
**Serial Console**  
You can connect to serial console, using Serial Android App
or apps like MobaXterm on windows  

Once connected via serial, type:
```bash
ifconfig
```
Look for the `inet addr:` value. That is your SJC IP.

#### Connect via SSH:
```bash
ssh root@<SJC_IP>
# Default password: hak5shark
```

### Step 3: Upload the Payload
From your laptop, copy the script to the Shark Jack:
```bash
# Linux/Mac/Windows (PowerShell with OpenSSH)
scp payload.sh root@<SJC_IP>:/root/payload/payload.sh
```

### Step 4: Fix the System Clock (Optional)
The Shark Jack has no RTC battery. It defaults to 2021 on every boot.
Fix the clock before running the payload to ensure correct timestamps:
```bash
# Manual fix
date -s "YYYY-MM-DD HH:MM:SS"

# Automatic NTP sync (requires internet access via router)
ntpd -n -q -p pool.ntp.org
```

---

## Running the Payload

### Automatic Execution (Attack Mode)
1. Insert the RJ45 end to the target ( in this case the Router )
2. Flip the switch to **Attack Mode**
   (switch away from the USB-C connector).
3. The script will execute automatically.
4. Wait for `LED FINISH` (solid green) before unplugging and Serial message stating that scan is completed.

---

## Retrieving Loot

After the script completes:

### Option A: SCP (Command Line)
```bash
# Download entire loot folder to your laptop
scp -r root@<SJC_IP>:/root/loot/ ./sjc_loot/
```

### Option B: SFTP (FileZilla / WinSCP)
```
Protocol:  SFTP
Host:      <SJC_IP>
Port:      22
Username:  root
Password:  hak5shark
```
Navigate to `/root/loot/` and download the timestamped folder.

### Option C: Python Web Server (Quick Preview)
On the Shark Jack (via SSH):
```bash
cd /root/loot/
python3 -m http.server 8080
```
On your laptop browser: `http://<SJC_IP>:8080`
Click and download any file directly.

---

## Overview of Files Structure

### `SUMMARY.txt`
High-level overview of the scan session:
```
============================================
SCAN SUMMARY
============================================
Date: 20240521_143022
SJC IP: 192.168.1.X
Gateway: 192.168.1.X
Subnet: 192.168.1.0/24
Hosts Found: X
Primary Target: 192.168.1.X
============================================
```

### `alive_hosts.txt`
Nmap grepable format showing all discovered hosts:
```
Host: 192.168.1.X ()    Status: Up
Host: 192.168.1.X ()    Status: Up
```

### `gateway_scan.txt`
Detailed audit of the router/gateway:
```
PORT     STATE  SERVICE VERSION
53/tcp   open   domain  dnsmasq 2.80
80/tcp   open   http    lighttpd 1.4.53
443/tcp  open   https   lighttpd 1.4.53
```

### `detailed_audit_<IP>.txt`
Aggressive OS fingerprinting results:
```
Device type: general purpose
Running: Linux 4.X
OS CPE: cpe:/o:linux:linux_kernel:4
OS details: Linux 4.4 - 4.9
```

### `mdns_devices.txt`
MDNS broadcast captures revealing device identities:
```
_googlecast._tcp.local  → Chromecast Ultra
_http._tcp.local        → Printer Model XYZ
_apple-mobdev._tcp.local → iPhone
```

### `arp_traffic.txt`
ARP request/reply map:
```
192.168.1.5  > 192.168.1.1  who-has
192.168.1.1  > 192.168.1.5  is-at XX:XX:XX:XX:XX:XX
```

---

## Known Limitations

Read the `Penetration testing research report (Shark)` file to learn more about the enviroment, technical issues and more.

---

## References

1. Hak5. (2021). *Shark Jack Cable Documentation*.
   https://docs.hak5.org/shark-jack/

2. Lyon, G. (2024). *Nmap Reference Guide*.
   https://nmap.org/book/man.html

3. OpenWrt Project. (2024). *OpenWrt Archive*.
   https://archive.openwrt.org/

4. MITRE Corporation. (2024). *T1046: Network Service Scanning*.
   https://attack.mitre.org/techniques/T1046/

5. MITRE Corporation. (2024). *T1557: Man-in-the-Middle*.
   https://attack.mitre.org/techniques/T1557/

6. Microsoft. (2023). *Remote NDIS (RNDIS) Design Guide*.
   https://learn.microsoft.com/en-us/windows-hardware/drivers/network/remote-ndis--rndis-

7. Silicon Labs. (2024). *CP210x USB to UART Bridge VCP Drivers*.
   https://www.silabs.com/developers/usb-to-uart-bridge-vcp-drivers

8. Internet Engineering Task Force. (1997). *RFC 2131: DHCP*.
   https://datatracker.ietf.org/doc/html/rfc2131

9. Internet Engineering Task Force. (1982). *RFC 826: ARP*.
   https://datatracker.ietf.org/doc/html/rfc826

10. OWASP Foundation. (2024). *Vulnerability Scanning*.
    https://owasp.org/www-community/Vulnerability_Scanning

---

## Author

**Szymon Stefański**

## License
This project is for **educational use only**.
Redistribution or use in unauthorized environments is strictly prohibited.