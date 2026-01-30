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



# Raspberry Pi RAID 1 NAS Setup Guide

This guide walks you through setting up a RAID 1 NAS using a Raspberry Pi, USB drives, and Samba for network file sharing.

---

## 1. Setting Up the Raspberry Pi

1. **Download Raspberry Pi OS**
   - Get the [Raspberry Pi Imager](https://www.raspberrypi.com/software/) and flash Raspberry Pi OS to your microSD card.

2. **Boot & Initial Configuration**
   - Insert the microSD card, connect peripherals, and power on.
   - Complete the first boot setup (network, locale, user).

3. **Update the System**
   ```bash
   sudo apt update
   sudo apt upgrade
   ```
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


---

## 2. Install Necessary Packages

- **mdadm** (for RAID management)
  ```bash
  sudo apt install mdadm
  ```
- **Samba** (for file sharing)
  ```bash
  sudo apt install samba
  ```

---

## 3. Set Up RAID 1 Array

1. **Connect USB Drives**
   - Use `lsblk` to verify both are detected.

2. **Partition Drives (if needed)**
   - Use `fdisk` or `parted` for partitioning.

3. **Create RAID 1 Array**
   ```bash
   sudo mdadm --create --verbose /dev/md0 --level=1 --raid-devices=2 /dev/sda1 /dev/sdb1
   ```
   - Replace `/dev/sda1` and `/dev/sdb1` as appropriate.

4. **Monitor RAID Creation**
   ```bash
   cat /proc/mdstat
   ```

5. **Create Filesystem**
   ```bash
   sudo mkfs.ext4 /dev/md0
   ```

6. **Mount RAID Array**
   ```bash
   sudo mkdir -p /mnt/raid1
   sudo mount /dev/md0 /mnt/raid1
   ```

7. **Persist Mount**
   - Add to `/etc/fstab`:
     ```
     /dev/md0 /mnt/raid1 ext4 defaults 0 0
     ```

8. **Repair Mount on new OS**
     ```
     sudo mdadm --assemble --scan
     ```

---

## 4. Configure Samba

1. **Edit Samba Configuration**
   ```bash
   sudo nano /etc/samba/smb.conf
   ```
   - Add at the end:
     ```ini
     [RAID1]
     path = /mnt/raid1
     available = yes
     valid users = pi
     read only = no
     browsable = yes
     public = yes
     writable = yes
     ```

2. **Set Samba Password**
   ```bash
   sudo smbpasswd -a pi
   ```

3. **Restart Samba**
   ```bash
   sudo systemctl restart smbd
   ```

---

## 5. Accessing Your NAS

- **Windows:**  
  Open File Explorer and go to `\\<RaspberryPi_IP_Address>\RAID1`

- **macOS/Linux:**  
  Use a file manager or terminal with `smb://<RaspberryPi_IP_Address>/RAID1`

---

## 6. Maintenance & Monitoring

- **Check RAID Status**
  ```bash
  sudo mdadm --detail /dev/md0
  ```
- **Monitor Disk Usage**
  ```bash
  df -h
  ```
- **Backup**  
  Regularly back up important data externally.

- **SMART Disk Monitoring**  
  Consider installing `smartmontools` for drive health:
  ```bash
  sudo apt install smartmontools
  sudo smartctl -a /dev/sda
  ```

- **RAID Email Alerts**  
  Configure mdadm to send status emails for RAID events (see mdadm documentation).

- **Expand RAID**  
  To add more drives or replace failed ones, refer to official mdadm and Raspberry Pi documentation.

---

## References

- [mdadm documentation](https://man7.org/linux/man-pages/man8/mdadm.8.html)
- [Samba documentation](https://www.samba.org/samba/docs/)
- [Raspberry Pi Docs](https://www.raspberrypi.com/documentation/)

---

**Tip:** Always test your backups and RAID recovery procedures before relying on this setup for critical data.
