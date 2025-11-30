📋 Lindows AU V1.2: Technical Specification Sheet

    "The Appliance OS: Built to Save E-Waste."

    Version: 1.2 (Gold Master)

    Codename: "Nuclear Noble"

    Build Date: November 30, 2025

    Architecture: amd64 (x86_64)

    Status: PRODUCTION READY

📥 Artifact Downloads

The following artifacts are hosted on the project's secure distribution mirror.
Artifact	Filename	Direct Link	Size
Operating System	Lindows_AU_V1.2.iso	Download ISO ([https://demo.spers.online/Lindows_AU_V1.2.iso](https://storage.googleapis.com/demo.spers.online/Lindows_AU_V1.2.iso))	4.63 GiB
Checksum File	Lindows_AU_V1.2.md5	Download MD5 https://storage.googleapis.com/demo.spers.online/Lindows_AU_V1.2.md5	60 Bytes
Build Log	building.log	View Build Log https://storage.googleapis.com/demo.spers.online/building.log	Text
Log Checksum	building.log.md5	View Log MD5 https://storage.googleapis.com/demo.spers.online/building.log.md5	47 Bytes

⚙️ System Architecture

Core Foundation

    Base System: Xubuntu 24.04.3 LTS (Noble Numbat)

    Kernel: Linux 6.8.0-generic (Optimized for broad hardware compatibility)

    Desktop Environment: XFCE 4.18 (Customized)

    Window Manager: Xfwm4 (Chicago95 Theme)

    Bootloader: GRUB2 (Hybrid MBR/EFI with ignore_uuid patch for maximum USB compatibility)

The "Lindows" Experience (Customizations)

    Visual Identity:

        Theme: Chicago95 (Windows 98 Aesthetic) enforced via /etc/xdg XML injection.

        Start Button: Custom Red/Gold "Lindows" branding.

        Layout: Taskbar locked to Bottom Center (Windows standard).

        Wallpaper: "Lindows Bliss" (Generated Gradient with Branding).

    Accessibility First:

        Smart Resolution Wizard: A custom Python tool (resolution_wizard.py) runs on first boot to safely scale text for seniors (includes 15-second auto-revert safety net).

        No "Untrusted" Errors: Desktop icons are pre-marked as trusted/executable system-wide.

🛡️ Security & "Clean IP" Protocol

    Zero Telemetry: All Ubuntu crash reporting (whoopsie, apport) and "Snap" telemetry have been purged.

    The Walled Garden:

        Snap Store: Removed.

        Default Store: gnome-software configured for Flatpak only. This prevents users from accidentally breaking the system with unverified DEB packages.

    Font Licensing:

        Microsoft Fonts: NOT Installed by default (avoids EULA legal traps).

        The "Alias" Trick: System is configured to seamlessly substitute Arial/Times New Roman with open-source Liberation Fonts. Documents look correct automatically.

        Unlocker: Users can opt-in to install proprietary fonts via the Welcome Wizard if strictly required.

♻️ E-Waste Recovery Features

    The "Time Capsule" Bridge:

        Feature: A desktop shortcut ("Old Windows Files") automatically detects, mounts, and opens the largest NTFS partition (the old C: Drive).

        Benefit: Instant access to old photos/documents without manual mounting or root passwords.

    Driver Assistant:

        Pre-loaded: bcmwl-kernel-source (Broadcom Wi-Fi) and nvidia-common logic.

        Detection: A script (driver_detect.sh) runs at startup; if it sees legacy hardware, it prompts the user to install the specific proprietary driver.

🏗️ The Build Process ("Nuclear Option")

This ISO was not built using graphical tools. It was constructed using a reproducible Headless Bash Pipeline:

    Asset Generation: A script (v1_2_assets.sh) generates all custom Python/Bash scripts and themes on the host.

    Layer Stacking: The builder (build_nuclear_v12.sh) extracts the 3 layers of Xubuntu (minimal, standard, live) and merges them into a single unified filesystem.

    Injection: Assets are injected directly into /usr/local/bin and /etc/skel.

    Boot Rescue: The hidden EFI partition (efi.img) is surgically extracted from the source media and implanted into the new ISO to ensure UEFI boot support.

    Serialization: The ISO is packed using zstd compression (Level 15) for performance on USB 2.0 drives.

📝 Installation Guide

    Download the ISO and MD5.

    Verify the file integrity:

        Windows: CertUtil -hashfile Lindows_AU_V1.2.iso MD5

        Linux: md5sum -c Lindows_AU_V1.2.md5

    Burn to USB (8GB+) using Ventoy (Recommended) or Rufus (MBR Mode).

    Boot:

        Secure Boot: Must be DISABLED in BIOS.

        Legacy/CSM: Enabled (recommended for pre-2015 hardware).

    Enjoy: Look for the "Install Lindows AU" icon on the desktop.
