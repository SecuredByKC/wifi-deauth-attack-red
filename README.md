# Portfolio-DeauthAttack 🔓📡
![Kali Linux](https://img.shields.io/badge/Tested%20On-Kali%20Linux%202025.1-blue?logo=kalilinux)
![VMware](https://img.shields.io/badge/Platform-VMware%20Workstation-lightgrey?logo=vmware)

This repository documents my **controlled Wi-Fi deauthentication (deauth) attack** and **WPA2 handshake capture** performed inside a **Kali Linux VM running on VMware Workstation Pro**. The exercise highlights how attackers can exploit unprotected 802.11 management frames to forcibly disconnect clients and capture WPA2 handshakes for offline password analysis. The purpose of this project is to provide a practical, hands-on example of offensive wireless testing, with future expansion into defensive monitoring strategies. While the attack itself is disruptive, it also offers valuable lessons for defenders who need to recognize and mitigate such activity in real-world environments.

**The objective:** force a client on **my own test network** to disconnect, capture the WPA2 handshake, and crack a weak passphrase with the `aircrack-ng` suite.

> ⚠️ **Ethical Notice:** Performed only on a network I own, strictly for education and portfolio purposes. Do **not** test on networks without explicit authorization.

---

## ⚙️ Lab Environment

- **Platform:** VMware Workstation Pro  
- **Guest OS:** Kali Linux (kali-rolling)  
- **VM Resources:** 8 GB RAM • 4 vCPUs • 80 GB storage  
- **Wi-Fi Adapters:** 2× **ALFA AWUS036ACM** (USB passthrough)  
  - `wlan0` → handshake capture  
  - `wlan1mon` → packet injection / deauth  
- **Tools:** `airmon-ng`, `airodump-ng`, `aireplay-ng`, `aircrack-ng`  
- **Wordlist:** `rockyou.txt`  

---

## 📸 Step-By-Step Process (Commands + Evidence)

### 1) Detect Wireless Adapters
    iwconfig
Identify available interfaces before starting.

![Detect Adapters](https://github.com/SecuredByKC/Portfolio-DeauthAttack/raw/main/01_detect_wireless_adapters.png)

---

### 2) Enable Monitor Mode (Injection Adapter)
    sudo airmon-ng start wlan0
Enable monitor mode on the adapter dedicated to injection; this creates `wlan1mon`.

![Enable Monitor Mode](https://github.com/SecuredByKC/Portfolio-DeauthAttack/raw/main/03_enable_monitor_mode.png)

---

### 3) Scan Nearby Wi-Fi Networks (Discovery)
    sudo airodump-ng wlan0
Discover SSIDs/BSSIDs and confirm the target (**KC’s Mobile WPA2 Network**).

![Scan Wi-Fi Networks](https://github.com/SecuredByKC/Portfolio-DeauthAttack/raw/main/04_scan_wifi_networks.png)

---

### 4) Target the Network and Capture the Handshake
    sudo airodump-ng --bssid <TARGET_BSSID> -c <CHANNEL> -w handshake wlan0
Lock onto the target AP on its channel and writes captured packets to `handshake-03.cap` using the listener interface.

![Target Network Capture](https://github.com/SecuredByKC/Portfolio-DeauthAttack/raw/main/05_target_network_airodump.png)

---

### 5) Deauthenticate a Client (Trigger Handshake)
    sudo aireplay-ng --deauth 10 -a <TARGET_BSSID> -c <CLIENT_MAC> wlan1mon
Send deauth frames from the injection adapter to force a reconnect, generating the WPA2 4-way handshake.

![Handshake Captured](https://github.com/SecuredByKC/Portfolio-DeauthAttack/raw/main/07_handshake_file_confirmed.png)

---

### 6) Crack the Captured Handshake
    aircrack-ng -w /usr/share/wordlists/rockyou.txt -b <TARGET_BSSID> handshake-03.cap
Run a dictionary attack with `rockyou.txt` against the handshake capture.

![Password Cracked](https://github.com/SecuredByKC/Portfolio-DeauthAttack/raw/main/08_password_cracked_aircrackng.png)

---

## 🖥️ VMware & Hardware Notes

- **USB Passthrough:** Both **ALFA AWUS036ACM** adapters are attached directly to the guest VM for full monitor/injection support.  
- **Networking:** VM set to **NAT** for package updates; wireless adapters are **not** bridged as NICs—each is passed through as a USB device to Kali.  
- **Driver Support:** Ensure chipset/driver support in Kali so `monitor` mode and injection function correctly.

---

## ✅ Key Takeaways

- Practical use of monitor mode and packet injection to capture WPA2 handshakes in a controlled lab.

- Developed a clear understanding of BSSID (access point) vs. client MAC (station) roles during deauthentication attacks.

- Demonstrated a successful dictionary attack, highlighting the risks of weak Wi-Fi passphrases.

- Built a reliable virtualized lab environment using VMware Workstation Pro and ALFA AWUS036ACM USB adapters for wireless testing.

---

## 📂 Repo Contents

- Step screenshots (evidence)  
- This `README.md` documenting methodology, tooling, and lab setup

---

## 🔮 Future Work

This project currently focuses on the offensive (red-team) side of wireless security: running a controlled deauthentication attack, capturing WPA2 handshakes, and performing a dictionary attack.

Planned enhancements include adding the defensive (blue-team) perspective, such as:

🟦 Wireshark Analysis – capturing traffic and using filters (e.g., wlan.fc.type_subtype == 12) to detect abnormal deauthentication frames

🟦 Kismet Alerts – demonstrating how intrusion detection tools flag suspicious deauth activity

🟦 Countermeasures – testing WPA3 and Protected Management Frames (802.11w) to show how they mitigate this type of attack

🟦 Monitoring Dashboards – documenting methods to log or visualize spikes in deauth/disassociation frames

These additions will help balance the red-team exercise with the blue-team strategies used in real-world wireless defense.

---

## ⚖️ Legal & Ethical Reminder

This methodology is provided for **defensive education**. Always obtain **explicit written permission** before testing any network you don’t own. Unauthorized access is illegal and unethical.

---

## 👤 Author

**Karim Cherradi**
GitHub: [@SecuredByKC](https://github.com/SecuredByKC)
