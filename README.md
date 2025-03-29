# nsupdater-cPanel
Bulk Update Nameservers in cPanel Zone Editor with Reliability and Backups! 

**Author:** Developed by **Dhruval Joshi from [HostingSpell.com](https://hostingspell.com/)**. | Personal: [TheDhruval.com](https://thedhruval.com/)
**GitHub:** [@thekugelblitz](https://github.com/thekugelblitz)  
This script was created by me & optimized by ChatGPT 4 later.

---

# 🧠 nsupdater v2

A powerful, interactive, and production-safe script to **bulk update NS (Nameserver) records** across all DNS zones on a cPanel WHM server.

---

## 🚀 Features

- 🔄 **Dynamic NS Replacement** – Supports any number of old and new NS entries.  
- 🔍 **Dry-Run Mode** – Preview changes before applying them.  
- ♻️ **Rollback Mode** – Restore all DNS zones from backup with one command.  
- 📁 **Centralized Backup** – All zone files are backed up to a timestamped folder.  
- 📊 **Summary Report** – Clear output of domains updated, skipped, or failed.  
- 🧾 **Log File** – All actions logged to `/root/nsupdater.log`.  
- 🧠 **Smart Exclusion** – Automatically excludes the domain associated with your new NS.  
- 🎨 **Color-coded Output** – Easy-to-read terminal feedback.

---

## 📥 What It Does

1. Prompts for the number of old and new NS entries.
2. Automatically excludes the domain associated with new NS (e.g., `host3k.com`).
3. Backs up all DNS zone files before any change to a timestamped folder.
4. Replaces old NS entries with new ones using `sed`.
5. Updates the DNS serial number to ensure propagation.
6. Reloads DNS configuration only once after all changes.
7. Logs all activity to `/root/nsupdater.log`.
8. It provides a final summary with counts and paths.

---

## 📥 Quick Install

From https://github.com/thekugelblitz/nsupdater-cPanel-WHM

```bash
wget https://raw.githubusercontent.com/thekugelblitz/nsupdater-cPanel-WHM/main/nsupdater.sh -O nsupdater_v2.sh
chmod +x nsupdater_v2.sh
```

Then run the script using:

```bash
./nsupdater_v2.sh
```

---

## ⚙️ Usage

Make the script executable:

```bash
chmod +x nsupdater_v2.sh
```

Run it as needed:

### ✅ Normal Mode (Apply changes)

```bash
./nsupdater_v2.sh
```

### 🧪 Dry-Run Mode (Simulate changes)

```bash
./nsupdater_v2.sh --dry-run
```

### 🔁 Rollback Mode (Restore backups)

```bash
./nsupdater_v2.sh --rollback
```

## 📂 File Structure

```
/root/
├── nsupdater_v2.sh                     # Main script
├── nsupdater.log                       # Action log
└── nsupdater_backup_YYYYMMDD_HHMM/     # Folder with backups
    └── domain.com.db.bak
```

---

## 🧰 Example Session

```bash
Enter the number of old NS entries: 2
Enter old NS entry 1: ns1.oldprovider.com.
Enter old NS entry 2: ns2.oldprovider.com.

Enter the number of new NS entries: 2
Enter new NS entry 1: ns1.host3k.com.
Enter new NS entry 2: ns2.host3k.com.

New NS domain will be excluded: host3k.com

Processing: clientdomain1.com
  ✔ Updated and serial bumped

Processing: clientdomain2.com
  ✔ Updated and serial bumped

──────────── Summary ─────────────
Total domains processed:     2
Updated successfully:        2
Skipped (dry-run or excluded): 0
Failed:                      0
Backup location:             /root/nsupdater_backup_20250328_1914/
Log file:                    /root/nsupdater.log
────────────────────────────────────
```

---

## 🛡️ Safety Checks

- Must be run as `root`
- Assumes `.db` zone files exist in `/var/named/`
- Automatically creates the backup folder and log file

---


## **📜 License**
This script is released under the **GNU GENERAL PUBLIC LICENSE Version 3**. You are free to modify and use it for commercial or personal use. Attribution is appreciated! 😊

---

## **🤝 Contribution**
Developed by **Dhruval Joshi** from **[HostingSpell](https://hostingspell.com)**  
GitHub Profile: [@thekugelblitz](https://github.com/thekugelblitz)

---
