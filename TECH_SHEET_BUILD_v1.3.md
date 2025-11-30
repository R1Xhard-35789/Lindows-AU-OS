Lindows AU V1.3: Technical Specification Sheet

    "The Appliance OS: Built to Save E-Waste."

    Version: 1.3 (Gold Master - Polished)

    Codename: "Bliss Protocol"

    Build Date: December 1, 2025

    Architecture: amd64 (x86_64)

    Status: PRODUCTION READY

📥 Artifact Downloads

The following artifacts are hosted on the project's secure distribution mirror.
Artifact	Filename	Direct Link
Operating System	Lindows_AU_V1.3.iso	Download ISO
Checksum File	Lindows_AU_V1.3.md5	Download MD5
Build Log	building.log	View Build Log
Log Checksum	building.log.md5	View Log MD5

⚙️ System Architecture

Core Foundation

    Base System: Xubuntu 24.04.3 LTS (Noble Numbat)

    Kernel: Linux 6.8.0-generic (Optimized for broad hardware compatibility)

    Desktop Environment: XFCE 4.18

    Window Manager: Xfwm4 (Chicago95 Theme Enforced)

    Bootloader: GRUB2 (Hybrid MBR/EFI with ignore_uuid patch for universal USB compatibility)

The "Lindows" Experience (V1.3 Features)

    Visual Identity:

        Theme: Chicago95 (Windows 95/98 Aesthetic) is now hard-coded into /etc/xdg/xfce4/xfconf to ensure every new user gets the correct look instantly.

        Wallpaper: "Lindows Bliss" (Custom Gradient with Gold Branding).

        Start Button: Custom Red/Gold "Lindows" branding.

        Taskbar: Locked to Bottom Center with "Start" label and icon.

    Accessibility First:

        Smart Resolution Wizard: A custom Python tool (resolution_wizard.py) scans hardware capabilities and offers safe scaling options with a 15-second auto-revert safety net.

        Trusted Desktop: All desktop icons are pre-marked as trusted, eliminating the "Untrusted Application Launcher" warning.

🛡️ Security & "Clean IP" Protocol

    Zero Telemetry: Ubuntu crash reporting (whoopsie, apport) and Snap telemetry are purged.

    The Walled Garden:

        Snap Store: Removed.

        Default Store: gnome-software configured for Flatpak only.

    Font Licensing:

        Microsoft Fonts: NOT Installed by default.

        The "Alias" Trick: System automatically substitutes Liberation Fonts for Arial/Times New Roman.

        Unlocker: A dedicated script (install_ms_fonts.sh) allows the user to optionally install proprietary fonts if required.

♻️ E-Waste Recovery Features

    The "Time Capsule" Bridge:

        Feature: A desktop shortcut ("Old Windows Files") automatically detects, mounts, and opens the largest NTFS partition (the old C: Drive).

        Benefit: Instant access to old photos/documents without manual mounting or root passwords.

    Driver Assistant:

        Pre-loaded: bcmwl-kernel-source (Broadcom Wi-Fi) and nvidia-common logic.

        Detection: A startup script (driver_detect.sh) scans for legacy hardware and prompts the user to install specific drivers if found.

🏗️ The Build Process ("Nuclear Option")

This ISO was constructed using a reproducible Headless Bash Pipeline:

    Asset Generation: v1_3_assets.sh generates all custom scripts, themes, and wallpapers on the host.

    Layer Stacking: The builder (build_nuclear_v12.sh) extracts the 3 layers of Xubuntu (minimal, standard, live) and merges them into a single unified filesystem.

    Injection: Assets are injected directly into /usr/local/bin and /etc/skel.

    Boot Rescue: The hidden EFI partition (efi.img) is surgically extracted from the source media and implanted into the new ISO.

    Serialization: The ISO is packed using zstd compression (Level 15) for performance on USB 2.0 drives.
