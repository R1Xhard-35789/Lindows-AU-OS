# **📐 DESIGN\_NOTES.md: Architectural Decisions**

**"Stability over Novelty. Privacy over Convenience."**

This document outlines the key pivots in the development of Lindows AU, documenting why specific technologies were chosen or abandoned during the Alpha build phase.

---

## **I. The Foundation Pivot: Base OS**

### **❌ Initial Choice: Ubuntu 24.04 LTS Minimal**

* **Concept:** Start with a bare-bones server image and install only the XFCE desktop to keep the ISO size small (\< 2GB).  
* **Failure Mode:**  
  * **Bootloader "Ghosts":** The Minimal ISO uses a different internal structure (`minimal.squashfs` vs `filesystem.squashfs`). This caused persistent "No medium found" errors (`/dev/sr0`) on live USBs because GRUB was looking for a server filesystem that didn't exist in the desktop context.  
  * **Missing Dependencies:** Critical tools like `add-apt-repository`, `curl`, and `git` were missing, causing build scripts to crash repeatedly.

### **✅ Final Choice: Xubuntu 24.04 LTS Desktop**

* **Reasoning:**  
  * **Native Architecture:** Xubuntu is pre-configured with XFCE. This eliminated the need to manually install the desktop environment, reducing build time and potential configuration errors.  
  * **Standard Boot Path:** It uses the standard `filesystem.squashfs` structure, ensuring compatibility with Rufus, Etcher, and legacy BIOS/UEFI bootloaders without manual hacking.  
  * **Efficiency:** While larger (3GB source), it strips down to a very efficient runtime.

---

## **II. The "Clean IP" Strategy: Fonts & EULA**

### **❌ Initial Choice: Microsoft Core Fonts (`ttf-mscorefonts-installer`)**

* **Concept:** Install the actual `.ttf` files for Arial, Times New Roman, etc., to ensure documents look perfect.  
* **Failure Mode:**  
  * **The EULA Trap:** The installer requires an interactive "Accept" (Yes/No) prompt. This broke the automated build script, pausing the cloud server indefinitely.  
  * **Legal Risk:** As developers, we cannot legally accept a Microsoft EULA on behalf of future users. It created a "Gray Market" distribution issue.

### **✅ Final Choice: Liberation Fonts \+ XML Aliasing**

* **Implementation:** We installed the open-source **Liberation Fonts** family (which are metrically identical to Microsoft fonts).  
* **The "Redmond Lie":** We injected a custom XML configuration (`30-metric-aliases-ms.conf`) into `/etc/fonts/conf.d/`.  
* **Result:** When an application asks for "Times New Roman," the system silently hands it "Liberation Serif." The document looks identical, but no Microsoft code or license is involved.  
* **Status:** **100% FOSS Compliance.**

---

## **III. The Browser Pivot**

### **❌ Initial Choice: Brave Browser**

* **Concept:** A privacy-focused, ad-blocking browser that looks like Chrome (familiarity).  
* **Failure Mode:**  
  * **Repo Instability:** The external Brave repositories caused GPG key errors ("Insecure Memory") inside the `chroot` build environment.  
  * **Complexity:** Adding external repos increased the risk of breakage during future updates.

### **✅ Final Choice: Firefox (Default)**

* **Reasoning:**  
  * **Stability:** It is the default Ubuntu browser, ensuring guaranteed security updates for 5 years.  
  * **Privacy:** It aligns with the "Zero Telemetry" ethos.  
  * **Simplicity:** It removes a massive external dependency from the build script.

---

## **IV. Safety & Partitioning**

### **❌ Initial Choice: Custom "Clean Break" Scripts**

* **Concept:** Scripts like `remove_windows.sh` and `Uninstall_Lindows.ps1` to help users wipe their drives easily.  
* **Failure Mode:** **High Risk.** A simple user error could wipe the wrong drive. Maintaining these scripts across different Windows versions (8, 10, 11\) is unsustainable for a small team.

### **✅ Final Choice: Deferred Automation**

* **Action:** These scripts were **deleted** from the repository.  
* **Strategy:** We currently rely on the **Calamares Installer**'s built-in partition manager (Manual Mode).  
* **Future:** Phase 3 will introduce a custom Python module for Calamares to handle this safely within the GUI.

---

## **V. The "Driver Assistant" Innovation**

### **The Problem**

Legacy hardware (Broadcom Wi-Fi, Old NVIDIA cards) often requires proprietary drivers that cannot be legally pre-installed or generally applied.

### **The Solution**

We built a "Lazy Detection" system:

1. **Detection (`driver_detect.sh`):** Runs silently on first boot. Scans `lspci` and `dmesg` for known hardware IDs (e.g., `14e4:43`).  
2. **Notification:** If (and ONLY if) it finds a match, it places a **"Hardware Assistant"** icon on the desktop.  
3. **Resolution:** The user clicks the icon to launch a simplified installer for that specific driver.  
* **Result:** Users with standard hardware never see it (Clean UI). Users with difficult hardware get a guided path to a fix.

