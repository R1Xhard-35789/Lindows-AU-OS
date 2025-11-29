# ***🇦🇺 Lindows AU: The Forever OS***

***"We don't throw away perfectly good computers just because a corporation said so."***

---

*\[\!WARNING\]*

## ***⚠️ ZERO BUILD STATUS: TEST AT YOUR OWN PERIL ⚠️***

***This is Pre-Alpha Software (v0.0.1).***

*This operating system contains scripts that **automatically modify disk partitions** and **permanently erase data** (e.g., in the "Clean Break" utility). It is intended for testing on **dedicated spare hardware** or **Virtual Machines** only.*

* ***DO NOT** install this on your primary work or school computer.*  
* ***NO WARRANTY** is provided. Use of this software is entirely at the user's risk.*

***YOU HAVE BEEN WARNED.***

---

## ***📜 The Manifesto: Saving E-Waste***

***Lindows AU** is a custom operating system architected in Queensland, Australia, with a singular mission: **To extend the life of aging x86 hardware.***

*We believe in the **Right to Repair**. By replacing heavy, spying operating systems with a lightweight, privacy-focused Linux core (Ubuntu 24.04 LTS), we prevent functional machines from entering the landfill.*

### ***Core Pillars***

* ***Muscle Memory:** The interface is familiar, using a clean **Windows 98 aesthetic** to reduce the learning curve for non-technical users.*  
* ***Privacy First:** We collect **Zero Telemetry**.*  
* ***The Walled Garden:** The App Store is restricted to **Flatpak** applications only for maximum security.*

---

## ***🖥️ The User Experience ("The Appliance")***

### ***Key Features***

* ***The Digital Suitcase:** Our proprietary tool allows users to transfer passwords and bookmarks from an existing Windows installation to Lindows via a local USB drive—no cloud syncing required.*  
* ***Sleeper Build:** While it looks like an appliance, it is a fully capable Ubuntu workstation underneath. The Terminal and advanced tools are hidden but accessible for technicians.*  
* ***The End of Life Switch:** The `/scripts/donation_mode.sh` script prepares the machine for donation, securely wiping personal data and unlocking the full Workstation environment.*

### ***Included Software (The Essentials)***

* ***Internet:** Brave Browser (Default) and Firefox (Alternative).*  
* ***Office:** Word Processor, Spreadsheet, Slideshow (LibreOffice).*  
* ***Creative:** Paint (Pinta) and Photo Editor (GIMP).*  
* ***Communication:** Zoom, Skype, Telegram, TeamViewer (Remote Support).*  
* ***Fun:** Solitaire, Minesweeper, 3D Pinball.*

---

## ***🛠️ Installation***

### ***Prerequisites***

* *A USB Stick (8GB+).*  
* ***Rufus** (to burn the ISO).*  
* *An older PC (Windows 7/10 era).*

### ***📥 Download the Production ISO***

*The official Lindows AU ISO (v0.0.1) is hosted securely on Google Cloud Storage.*

1. ***Download ISO (6.2 GB):** `https://demo.spers.online/ubuntu-24.04.3-2025.11.28-desktop-amd64.iso`*  
2. ***Download MD5 Checksum (for Verification):** `https://demo.spers.online/ubuntu-24.04.3-2025.11.28-desktop-amd64.md5`*

### ***Verification Protocol***

1. *Download both files.*  
2. *On Windows, open Command Prompt and run: `CertUtil -hashfile [FILENAME].iso MD5`*  
3. *Compare the resulting code with the content of the `.md5` text file.*

### ***Installation Instructions***

1. *Use Rufus to flash the ISO to a USB drive.*  
2. *Boot your target PC from the USB.*  
3. *Follow the Calamares installer wizard.*

---

### 📝 The Lindows Guarantee (Open Standards)

* **No EULA Required:** This operating system utilizes **Liberation Fonts**, an open-source typeface family metrically compatible with Microsoft's proprietary fonts. This ensures documents look correct without requiring you to agree to a Microsoft license to use your own computer.

## ***⚖️ Legal & Disclaimer***

***Lindows AU** is a revival and tribute to the original open-source spirit.*

* ***Transparency Note:** We registered the domain **Lindows.com.au** for this local community project before fully re-discovering the name's contentious legal history (circa 2004). We proceed with humility, maintaining that this is a non-commercial, 'Right to Repair' initiative, and hope not to be stepping on any corporate toes.*  
* *We are **not affiliated** with Microsoft Corporation. The name "Lindows" is used here as a descriptive term for a "Linux-Windows Hybrid" workflow concept.*

***Built with ❤️ and ☕ in Bundaberg, Queensland.** Saving the planet, one Pentium at a time.*

