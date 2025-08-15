# 🗄️ LinuxRAID-NAS  
Raspberry Pi NAS with Samba, RAID 1

A low-cost, home cloud storage solution built with Raspberry Pi.  
Supports remote file access via Samba, data redundancy with RAID 1.  
Ideal for personal backups, media storage, and secure file sharing.

---

## 📦 Prerequisites

**Hardware**
- Raspberry Pi 4 (recommended) or Pi 3  
- 2 × identical USB hard drives / SSDs (for RAID 1)  
- MicroSD card (16GB+)  
- Raspberry Pi power supply  
- Ethernet cable or Wi-Fi

**Software**
- `Raspberry Pi OS` (Lite recommended)  
- `mdadm` (RAID management)  
- `samba` (file sharing)  
- `Python 3` (Automation Script) 

---


## 🗄️ Raid 1 Array
```bash
      +------------------+
      |   Raspberry Pi   |
      |   (Linux NAS)    |
      +--------+---------+
               |
               | mdadm (RAID 1)
               |
       +-------+-------+
       |               |
+------+-----+   +-----+------+
| Drive 1    |   | Drive 2    |
| (/dev/sda) |   | (/dev/sdb) |
| Data Block |   | Data Block |
| Data Block |   | Data Block |
+------------+   +------------+

RAID 1 = Mirroring → Identical copies of all data
```
---


## ⚙️ Setup Instructions

### 1️⃣ Connect to Raspberry Pi via SSH
```bash
ssh <username>@<hostname>.local
ssh <username>@<ip address>
# Example:
ssh divyam@nas.local
```

## ⚙️ Installation Steps

### 1️⃣ Prepare the Raspberry Pi
```bash
# Update & upgrade packages
sudo apt update && sudo apt upgrade -y
```
