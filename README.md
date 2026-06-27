# dedicated-kali-linux-machine
A fully documented hardware revival and Linux deployment project taking a 2015 ASUS laptop from ransomware-infected closet relic to a networked, always-on Kali Linux penetration testing machine.
# 🐉 SYKES-KALI — Dedicated Kali Linux Security Lab Machine

![Kali Linux](https://img.shields.io/badge/Kali_Linux-6.18.12-557C94?style=for-the-badge&logo=kalilinux&logoColor=white)
![SSH](https://img.shields.io/badge/Remote_Access-SSH-4CAF50?style=for-the-badge&logo=openssh&logoColor=white)
![Status](https://img.shields.io/badge/Status-Active-brightgreen?style=for-the-badge)
![Hardware](https://img.shields.io/badge/Hardware-Repurposed-orange?style=for-the-badge)

> A fully documented hardware revival and Linux deployment project taking a 2015 ASUS laptop from ransomware-infected closet relic to a networked, always-on Kali Linux penetration testing machine.

---

## 📋 Table of Contents

- [Project Overview](#-project-overview)
- [Hardware Specifications](#-hardware-specifications)
- [The Problem](#-the-problem)
- [Build Process](#-build-process)
  - [Phase 1 — Hardware Assessment](#phase-1--hardware-assessment)
  - [Phase 2 — SSD Swap](#phase-2--ssd-swap)
  - [Phase 3 — BIOS Configuration](#phase-3--bios-configuration)
  - [Phase 4 — Kali Linux Installation](#phase-4--kali-linux-installation)
- [Post-Installation Configuration](#-post-installation-configuration)
- [Network Topology](#-network-topology)
- [Tools Installed](#-tools-installed)
- [Planned Use Cases](#-planned-use-cases)
- [Skills Demonstrated](#-skills-demonstrated)
- [Home Lab Ecosystem](#-home-lab-ecosystem)

---

## 🎯 Project Overview

**SYKES-KALI** is a repurposed ASUS X550ZA laptop rebuilt from the ground up as a dedicated Kali Linux cybersecurity lab machine. The system was recovered from storage in 2026, diagnosed for hardware health, upgraded with a new SSD, and deployed with a full Kali Linux installation.

The machine operates as a **standalone penetration testing node** within my home lab network — SSHing into it remotely from my main Windows workstation, with no need to physically interact with the laptop for day-to-day security practice.

This project was completed on **June 26, 2026** as part of an ongoing effort to build a hands-on cybersecurity portfolio targeting federal IT contracting roles in the DC metro area.

---

## 💻 Hardware Specifications

| Component | Details |
|---|---|
| **Device** | ASUS X550ZA (X550ZA-RB11) |
| **Manufactured** | April 2015 |
| **CPU** | AMD A10-7400P Radeon R6 — 4 Cores, up to 3.2GHz |
| **RAM** | 8GB DDR3 SODIMM (~6.9GB usable) |
| **Storage** | Fanxiang S101Q **256GB SATA SSD** *(upgraded)* |
| **Original Storage** | WD Blue 1TB HDD 5400RPM *(repurposed as backup drive)* |
| **GPU** | AMD Radeon R6 Integrated |
| **Wi-Fi** | MediaTek MT7630E 802.11bgn |
| **Ethernet** | Realtek RTL8111 Gigabit |
| **OS** | Kali Linux 6.18.12 — Xfce Desktop, UEFI Mode |
| **Hostname** | `SYKES-KALI` |
| **IP Address** | `192.168.1.224` (DHCP via Wi-Fi) |

---

## 🔍 The Problem

This laptop had been sitting in storage for years — last used for music production in high school. When retrieved, it had several issues:

- ❌ **Running Windows 10** — end of life, no security updates
- ❌ **Trackpad non-functional** — hardware damage from a prior virus infection
- ❌ **100% disk usage at idle** — spinning HDD being strangled by Windows background processes
- ❌ **Active ransomware infection from 2019** — Documents folder encrypted by Globe/Purge ransomware variant (`[unlockmeplease@...]` file extension)
- ❌ **Stripped retaining screw** on the drive bay

Despite these issues, CrystalDiskInfo SMART analysis confirmed the original HDD was **mechanically healthy** — making it a candidate for repurposing rather than disposal.

---

## 🔧 Build Process

### Phase 1 — Hardware Assessment

Before touching anything, the system was booted and assessed:

**SMART Health Check (CrystalDiskInfo)**

```
Drive:    WDC WD10JPVX-00JC3T0 — 1000.2GB
Health:   ✅ GOOD
Reallocated Sectors:      0
Pending Sectors:          0
Uncorrectable Sectors:    0
Power On Hours:           6,115
Temperature:              41°C
```

The 100% disk idle usage was **not** a drive failure — it was Windows 10 running indexing and background services on a slow 5400RPM spinning disk. The drive was physically clean.

**Ransomware Triage**

The Documents folder contained 94 encrypted files dated July 30–31, 2019, consistent with the **Globe2 ransomware** family. A decryptor check via [No More Ransom](https://www.nomoreransom.org) returned no available solution. Files were written off and the drive was scheduled for a full format wipe before repurposing.

---

| Photo | Description |
|---|---|
| ![Closed lid](<./images/01-closed-lid.jpeg>) | ASUS X550Z pulled from storage |
| ![Internals](<./images/04-internals-exposed.jpeg>) | Bottom panel removed, original WD Blue HDD visible |

---

### Phase 2 — SSD Swap

The original WD Blue HDD was replaced with a **Fanxiang S101Q 256GB SATA SSD**.

**Why 256GB?**
- Kali Linux full install: ~20GB
- Top 10 tools + default package: ~5GB
- SecLists wordlist collection: ~1GB+
- Room for additional tools, captures, and practice files
- 120GB would get tight within months of active use

**Drive repurpose plan for the old 1TB HDD:**
- Full format (NTFS, 32KB allocation units, non-quick format to overwrite all sectors)
- Placed in a USB 2.5" enclosure
- Repurposed as an offsite cold backup for Jellyfin anime media library on SYKESHOMESERVER

---

| Photo | Description |
|---|---|
| ![SSD installed](<./images/06-ssd-installed.jpeg>) | Fanxiang 256GB SSD seated in the drive bay with original bracket |

---

### Phase 3 — BIOS Configuration

First boot with the new blank SSD dropped into BIOS Setup (expected — no OS present).

**Changes made:**

| Setting | Before | After | Reason |
|---|---|---|---|
| Fast Boot | Enabled | **Disabled** | Allow USB installer detection |
| Launch CSM | Disabled | Disabled ✅ | UEFI mode confirmed |
| Secure Boot | Enabled | **Disabled** | Kali bootloader is unsigned |

---

| Photo | Description |
|---|---|
| ![BIOS](<./images/07-bios-post.jpeg>) | BIOS confirming A10-7400P, 8192MB RAM — hardware recognized correctly |
| ![Secure Boot Error](<./images/08-secure-boot-violation.jpeg>) | Secure Boot Violation on first USB boot — resolved by disabling in Security tab |

---

### Phase 4 — Kali Linux Installation

**ISO source:** Existing Kali ISO used for VirtualBox VM — same file, just flashed to USB via Rufus (GPT/UEFI mode).

**Installer settings:**

```
Install type:      Graphical Install
Boot mode:         UEFI
Hostname:          SYKES-KALI
Username:          bsykes
Partition method:  Guided — use entire disk
Partition scheme:  All files in one partition
  ├── sda1: ESP (EFI boot)
  ├── sda2: ext4 (main OS)
  └── sda3: swap
Desktop env:       Xfce (lightweight, optimal for A10-7400P)
Tool packages:     ✅ top10  ✅ default recommended
Network:           Skipped during install (configured post-boot via Wi-Fi)
```

**Why Xfce over GNOME/KDE?**
GNOME and KDE Plasma are resource-heavy desktop environments. On an A10-7400P with 8GB DDR3, they would feel sluggish. Xfce is Kali's default for a reason — lightweight, fast, and purpose-built for security work rather than aesthetics.

---

| Photo | Description |
|---|---|
| ![Kali installer](<./images/09-kali-installer.jpeg>) | Kali Linux installer menu in UEFI mode |
| ![Kali desktop](<./images/12-kali-desktop.jpeg>) | SYKES-KALI fully booted into Kali Xfce desktop |

---

## ⚙️ Post-Installation Configuration

### 1. System Update

First command run after reaching the desktop:

```bash
sudo apt update && sudo apt upgrade -y
```

### 2. SSH Remote Access

Enabled SSH and set it to start on boot — allowing full remote management from the main Windows workstation upstairs via PowerShell, without needing to touch the laptop:

```bash
sudo systemctl enable ssh
sudo systemctl start ssh
```

**Connecting from Windows PowerShell:**

```powershell
ssh bsykes@192.168.1.224
```

Output confirms connection:
```
Linux SYKES-KALI 6.18.12+kali-amd64 #1 SMP PREEMPT_DYNAMIC Kali 6.18.12-1kali1 (2026-02-25)
(bsykes@SYKES-KALI)-[~]$
```

### 3. Disable Sleep / Suspend

Masked all sleep targets to keep SYKES-KALI always-on and SSH accessible — critical for a machine used as a remote lab node:

```bash
sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
```

To re-enable:
```bash
sudo systemctl unmask sleep.target suspend.target hibernate.target hybrid-sleep.target
```

---

## 🌐 Network Topology

```
Internet
    │
    ▼
WiFi Router (192.168.1.1)
    │
    ├──── SYKESHOMESERVER (192.168.1.x) — Windows 11 Pro
    │         └── Docker: Pi-hole, Jellyfin, Plex, Pelican
    │         └── VMs: Active Directory, Security Onion, osTicket
    │
    ├──── Personal PC (192.168.1.x) — Windows 11
    │         └── SSH Client → SYKES-KALI
    │
    └──── SYKES-KALI (192.168.1.224) — Kali Linux ← YOU ARE HERE
```

---

## 🛠️ Tools Installed

Kali's `top10` and `default` packages include:

| Tool | Category |
|---|---|
| **Nmap** | Network scanning & enumeration |
| **Metasploit Framework** | Exploitation & post-exploitation |
| **Burp Suite** | Web application testing |
| **Wireshark** | Packet capture & analysis |
| **John the Ripper** | Password cracking |
| **Hashcat** | GPU-accelerated password cracking |
| **Aircrack-ng** | Wireless security testing |
| **SQLmap** | SQL injection automation |
| **Nikto** | Web server vulnerability scanner |
| **Hydra** | Network login brute forcing |

---

## 🎯 Planned Use Cases

- **Penetration testing practice** — running Kali tools against intentionally vulnerable VMs (Metasploitable, DVWA)
- **Active Directory attack simulation** — targeting the AD lab on SYKESHOMESERVER (controlled environment)
- **TryHackMe / HackTheBox** — CTF-style challenges via SSH tunnel
- **Network scanning** — Nmap enumeration of home lab network segments
- **Packet analysis** — Wireshark captures during brute force simulations against Security Onion
- **SOC Tier 1 skill building** — log analysis, alert triage, MITRE ATT&CK mapping
- **Post-Security+ hands-on reinforcement** — bridging certification knowledge to practical execution

---

## 📊 Skills Demonstrated

| Skill | Evidence |
|---|---|
| **Hardware Diagnostics** | CrystalDiskInfo SMART analysis, component identification |
| **Physical Repair** | Laptop disassembly, SSD replacement, stripped screw extraction |
| **BIOS/UEFI Configuration** | Secure Boot disable, CSM, Fast Boot, boot order |
| **Linux Deployment** | Kali installation, partitioning, package selection |
| **Network Administration** | SSH setup, IP management, remote system access |
| **Security Incident Response** | Ransomware triage, malware identification, disk forensics |
| **System Administration** | Service management via systemctl, sleep/suspend hardening |
| **Technical Documentation** | Full build process photographed and documented |

---

## 🏠 Home Lab Ecosystem

SYKES-KALI is one node in a larger home lab environment:

| Machine | Role |
|---|---|
| **SYKESHOMESERVER** | Ryzen 5 5500, Win 11 Pro — Docker host, AD, Security Onion, osTicket |
| **Personal PC** | Main workstation — management, SSH client, development |
| **HP Victus** | Intel i7-12650H, RTX 3050 Ti — portable workstation |
| **SYKES-KALI** | ASUS X550ZA, Kali Linux — dedicated security lab node |

---

## 👤 Author

**Bryan Sykes**
- 🐙 GitHub: [github.com/MrBSykes](https://github.com/MrBSykes)
- 💼 LinkedIn: [linkedin.com/in/bryan-k-sykes](https://linkedin.com/in/bryan-k-sykes/)
- 📧 Email: bsykes@alumni.berklee.edu

---

*Built on June 26, 2026 — Alexandria, VA*

