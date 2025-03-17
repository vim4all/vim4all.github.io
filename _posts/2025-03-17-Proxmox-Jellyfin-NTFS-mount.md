---
title: "Using an NTFS HDD in Proxmox with Jellyfin and Smart TV"
date: 2025-03-17
description: "Guide on mounting an NTFS HDD in Proxmox for Jellyfin while keeping compatibility with a Smart TV."
categories: [Proxmox, Jellyfin, Storage]
tags: [NTFS, HDD, Proxmox, Jellyfin, Smart TV]
---

## **Using an NTFS HDD in Proxmox with Jellyfin and Smart TV**

If you have a **2TB NTFS HDD** with movies and want to use it in **Proxmox with Jellyfin**, while keeping it compatible with your **Smart TV**, follow this guide to set it up properly.

---

## **1. Connect and Identify the Drive**
1. Plug the **2TB HDD** into your Proxmox server.
2. Open a Proxmox **shell** (via web GUI or SSH).
3. Identify the drive using:
   ```bash
   lsblk
   ```
   Find your **2TB HDD**, likely named `/dev/sdX` (e.g., `/dev/sdb`).

---

## **2. Install NTFS-3G Support**
Proxmox does not support NTFS writes by default. Install **ntfs-3g**:
```bash
apt update
apt install ntfs-3g
```

---

## **3. Create a Mount Point & Auto-Mount**
### **Find the Partition UUID**
```bash
blkid
```
Look for your drive’s **UUID** (a long string like `1234-ABCD`).

### **Create a Mount Folder**
```bash
mkdir -p /mnt/movies
```

### **Edit fstab for Auto-Mount**
Edit `/etc/fstab`:
```bash
nano /etc/fstab
```
Add the following line (replace `UUID=your-uuid` with your actual UUID):
```bash
UUID=your-uuid /mnt/movies ntfs-3g defaults,uid=1000,gid=1000,umask=0022 0 0
```
Save (CTRL+X → Y → ENTER).

**Test it by running:**
```bash
mount -a
```

---

## **4. Pass the Drive to Jellyfin (LXC or VM Setup)**

### **If Using an LXC Container**
1. Edit the LXC Config (replace `100` with your container ID):
   ```bash
   nano /etc/pve/lxc/100.conf
   ```
2. Add this line at the end:
   ```bash
   mp0: /mnt/movies,mp=/mnt/movies
   ```
3. Restart the container:
   ```bash
   pct restart 100
   ```

### **If Using a VM**
1. Add the HDD as a **Passthrough Disk** to the VM:
   ```bash
   qm set 100 -virtio2 /dev/sdb
   ```
2. Inside the VM, mount it as in **Step 3**.

---

## **5. Configure Jellyfin to Use the Drive**
1. In Jellyfin, go to **Dashboard → Libraries**.
2. Click **Add Library**.
3. Choose **Movies** (or relevant type).
4. Set the folder to `/mnt/movies` (or the correct path in your LXC/VM).
5. Save & restart Jellyfin.

---

## **6. Unmount When Needed**
To use the HDD with your **Smart TV**, unmount it before unplugging:
```bash
umount /mnt/movies
```
Then safely disconnect the drive and plug it into your TV.

---

## **Conclusion**
Now, your **NTFS HDD** works with **Jellyfin on Proxmox** while staying **compatible with your Smart TV**! 🚀

