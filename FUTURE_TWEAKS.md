# **🗺️ Lindows AU: Future Tweaks & Roadmap**

This document outlines architectural improvements and feature additions planned for versions v0.0.2 and beyond. The focus remains on maximizing the "Appliance" feel while preserving underlying capability.

## **I. Phase 2: Aesthetic Polish & Authenticity**

The current build uses default Chicago95 assets. This phase focuses on fine-tuning the look and feel to surprise the end-user.

| Feature | Goal | Technical Challenge |
| :---- | :---- | :---- |
| **Screensaver Revival** | Install and configure `xscreensaver` (instead of the default XFCE screensaver). | Customizing the `xscreensaver` config file (`.xscreensaver`) to default to **3D Pipes**, **Maze**, and **Flying Toasters** for maximum nostalgia impact. |
| **Custom Wallpaper** | Bake the official "Lindows Bliss Hill" wallpaper into the ISO. | Must be set via the XFCE settings manager (`xfconf-query`) in `/etc/skel` to ensure it applies to all new users, overriding the teal default. |
| **Start Menu Icon** | Replace the generic XFCE "mouse" icon with a pixel-perfect **Lindows "Start" logo**. | Requires modifying the `xfce4-panel` configuration file (`whiskermenu-1.rc`) and ensuring the custom icon is present in the Chicago95 icon pack path. |
| **System Sounds** | Add classic Windows 95/98 sound events (startup chime, shutdown tone) to XFCE. | Requires creating and configuring custom `.wav` files via the XFCE settings panel. |

## **II. Phase 3: Automation & Technical Hardening**

These items involve solving core Linux integration challenges (e.g., using D-Bus) that were avoided in the initial build.

### **1\. The Partitioning Module (The "2/3rds Rule")**

The initial install relies on the generic Calamares "Install Alongside." We need to automate our specific partitioning logic.

* **Goal:** Hard-code the "2/3rds Rule" (66% EXT4 / 33% NTFS) behind a single "Install Lindows" button.  
* **Method:** Develop a custom **Python module for Calamares** that runs during the "partitioning" stage, calculates the exact split size of the free space, and forces the creation of the NTFS "Shared Bridge" partition.

### **2\. Flatpak Pre-Loading (Solving the Chroot Problem)**

The current build relies on the user downloading apps via the store after installation. This is slow.

* **Goal:** Install all 10 "Essential Apps" (Zoom, Pinta, GIMP, etc.) directly into the ISO at build time.  
* **Challenge:** Running `flatpak install` inside a `chroot` environment fails because the `systemd` and `D-Bus` services required for Flatpak are not running. This requires using **`qemu-static`** and complex unshared namespace methods, or mounting/faking a full `/var/lib/flatpak` directory structure *before* packaging.

### **3\. Legacy Hardware Support**

* **Goal:** Add support for notorious legacy components.  
* **Focus:** Integrating older, non-free wireless drivers (e.g., Broadcom) and proprietary graphics drivers (NVIDIA 340 series for very old cards) into the initial build to ensure maximum "Right to Repair" compatibility.

---

## **III. Phase 4: Distribution & Testing**

* **Automated Builds:** Set up a Continuous Integration (CI) pipeline (e.g., GitHub Actions) to automatically build the ISO whenever changes are merged to `main`. This eliminates the need for manual Cloud VM spinning.  
* **Live Testing Suite:** Develop a test suite to automatically verify that the `lindows_welcome.py` script runs, the desktop icons are correctly placed, and the "Walled Garden" (`gnome-software`) only shows Flatpaks.

