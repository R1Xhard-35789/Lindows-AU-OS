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

## **IV. Legacy Hardware & Driver Support (Addendum)**

This phase addresses the reality of our target hardware. While the base kernel is excellent, older systems (especially laptops) rely on proprietary or non-free components that require manual installation to function correctly. This is essential for the "Right to Repair" ethos.

### **1\. Wireless and Firmware Reliability**

We must ensure that Wi-Fi works out of the box on machines with notoriously difficult chipsets.

| Component | Goal | Technical Challenge |
| :---- | :---- | :---- |
| **Broadcom/Realtek** | Ensure full Wi-Fi functionality on older laptops. | The base ISO often lacks the necessary **non-free firmware** or the `bcmwl-kernel-source` package, which is necessary for these cards to function. The solution requires pre-installing or configuring the system to install these during the build process. |
| **Media Codecs** | Enable playback of common media files (MP3, H.264, AAC). | Licensing prevents these codecs from being included by default. We must integrate the **`ubuntu-restricted-extras`** package (or similar meta-packages) to guarantee functional media players like VLC upon first boot. |

### **2\. Older Graphics Driver Support**

For users who want to run the machine as a fully capable workstation, we must simplify the path for installing legacy proprietary graphics drivers.

* **NVIDIA Legacy:** Older NVIDIA cards (e.g., those requiring the **340 series** or **390 series** drivers) are dropped from mainline Ubuntu support over time. We need to document and/or pre-stage the necessary older driver packages and header files within the ISO structure, providing a simple, one-click script to install the correct legacy driver without breaking the XFCE desktop environment.



🗺️ Lindows AU: Future Tweaks & Roadmap

This document outlines architectural improvements and feature additions planned for versions v0.0.2 and beyond.

I. Phase 3: Aesthetic Polish & Authenticity (Next Priority)

This phase focuses on finalizing the visual identity of Lindows AU.
Feature	Goal	Technical Challenge
Screensaver Revival	Install and configure xscreensaver to default to 3D Pipes, Maze, and Flying Toasters.	Must create and set the default .xscreensaver config file for all new users.
Custom Wallpaper	Bake the official "Lindows Bliss Hill" wallpaper into the ISO.	Must be set via the XFCE settings manager (xfconf-query) in /etc/skel to apply to all new users.
Start Menu Icon	Replace the generic XFCE "mouse" icon with a pixel-perfect Lindows "Start" logo.	Requires modifying the whiskermenu-1.rc configuration.
Boot/Plymouth Screen	Replace the default Ubuntu logo with a retro Lindows branded progress bar.	Requires creating a custom Plymouth theme and setting it as the boot default.
System Sounds	Add classic Windows 95/98 sound events (startup chime, shutdown tone) to XFCE.	Requires configuring custom .wav files via the XFCE settings panel.

II. System Disclosure & Compliance

Component	Goal	Status in V0.0.1
Microsoft EULA	Inform the user of pre-acceptance.	DONE. The welcome script now informs the user that the EULA for Microsoft fonts was accepted during setup.
Partitioning Automation	Automate safe deletion of Windows and expansion of EXT4 partition during post-install (replacement for remove_windows.sh).	CRITICAL DEFERRED FEATURE. Requires custom Calamares Python module.
