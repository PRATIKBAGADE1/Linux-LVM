
# LVM Setup, Extension, and Management

This project demonstrates the complete workflow of setting up, partitioning, creating Logical Volumes using LVM, extending storage, and resizing filesystems in a Linux environment.  
It also explains how to remove an extended volume if needed.

---

## 🛠️ Steps Performed

1. **Added a new Hard Disk:** `/dev/sdb`
2. **Created a Partition:** `/dev/sdb1`
3. **Created a Physical Volume (PV):**  
   ```
   pvcreate /dev/sdb1
   ```
4. **Created a Volume Group (VG):**  
   ```
   vgcreate vgdata /dev/sdb1
   ```
5. **Created Two Logical Volumes (LVs):**
   - apps1 (Size: 500MB)
   - apps2 (Size: 500MB)
   ```
   lvcreate -L 500M -n apps1 vgdata
   lvcreate -L 500M -n apps2 vgdata
   ```
6. **Created Filesystems and Mounted LVs:**
   ```
   mkfs.ext4 /dev/vgdata/apps1
   mkfs.ext4 /dev/vgdata/apps2
   mkdir /apps1 /apps2
   mount /dev/vgdata/apps1 /apps1
   mount /dev/vgdata/apps2 /apps2
   ```

---

## 🔥 Extended the Storage

7. **Added Another Hard Disk:** `/dev/sdc` (1GB)
8. **Created Partition:** `/dev/sdc1`
9. **Extended the Volume Group (VG):**
   ```
   pvcreate /dev/sdc1
   vgextend vgdata /dev/sdc1
   ```

10. **Extended Logical Volume (apps2):**
    - Increased apps2 size to 2GB
    ```
    lvextend -L +1.5G /dev/vgdata/apps2
    resize2fs /dev/vgdata/apps2
    ```

---

## 🔄 Simple Diagram of the Setup

```
          +-----------------+
          |   /dev/sdb       |
          |   Partition: sdb1|
          +-----------------+
                   |
                   v
          +-----------------+
          |  Volume Group    |
          |     vgdata       |
          +-----------------+
               |         |
         +-----+         +-----+
         |                     |
+----------------+   +----------------+
| Logical Volume |   | Logical Volume  |
|     apps1      |   |     apps2       |
+----------------+   +----------------+
                          |
                    Extended by /dev/sdc1
```

---

## 🧹 How to Remove Extended Volumes

If you want to **remove the extended storage** from apps2:

1. **Backup data** if necessary.
2. **Shrink filesystem (carefully):**
   ```
   umount /apps2
   e2fsck -f /dev/vgdata/apps2
   resize2fs /dev/vgdata/apps2 500M
   lvreduce -L 500M /dev/vgdata/apps2
   mount /dev/vgdata/apps2 /apps2
   ```

3. **Remove physical volume:**
   ```
   vgreduce vgdata /dev/sdc1
   pvremove /dev/sdc1
   ```

4. **(Optional) Detach disk** `/dev/sdc` if no longer needed.

---

## 📚 Key Commands Summary

| Step                  | Command Example |
|------------------------|-----------------|
| Create PV              | `pvcreate /dev/sdb1` |
| Create VG              | `vgcreate vgdata /dev/sdb1` |
| Create LV              | `lvcreate -L 500M -n apps1 vgdata` |
| Extend VG              | `vgextend vgdata /dev/sdc1` |
| Extend LV              | `lvextend -L +1.5G /dev/vgdata/apps2` |
| Resize Filesystem      | `resize2fs /dev/vgdata/apps2` |
| Shrink Filesystem      | `resize2fs /dev/vgdata/apps2 500M` |
| Remove PV from VG      | `vgreduce vgdata /dev/sdc1` |

---
