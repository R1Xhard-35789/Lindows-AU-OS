📐 DESIGN_NOTES.md: Architectural Decisions & Pivots

    "Stability over Novelty. Privacy over Convenience."

This document records the critical architectural changes and design decisions made during the Alpha development of Lindows AU (V0.0.1 - V1.0). It serves as the historical record of why the system is built this way.

🔄 I. The Base OS Pivot

❌ Initial Choice: Ubuntu 24.04 LTS Minimal

    Concept: Start with a bare-bones server image and install only the XFCE desktop to keep the ISO size small (< 2GB).

    Failure Mode:

        Bootloader "Ghosts": The Minimal ISO uses a different SquashFS structure (minimal.squashfs) than the Desktop installer expects. This caused persistent /dev/sr0: No medium found errors on live USBs because GRUB was looking for a server filesystem that didn't exist in the desktop context.

        Missing Dependencies: Critical tools like add-apt-repository, curl, and git were missing, complicating the build script.

✅ Final Choice: Xubuntu 24.04 LTS Desktop

    Reasoning:

        Native XFCE: The desktop environment is pre-installed and optimized by the Xubuntu team. This eliminated the need to manually install the desktop, reducing build time and configuration errors.

        Standard Boot Path: It uses the standard filesystem.squashfs structure, ensuring 100% compatibility with Rufus, Etcher, and legacy BIOS/UEFI bootloaders without manual hacking.

        Efficiency: While larger (3GB source), it strips down to a very efficient runtime for older hardware.

⚖️ II. The "Clean IP" Strategy (Legal & EULA)

❌ Initial Choice: Pre-Installed Microsoft Fonts

    Concept: Install ttf-mscorefonts-installer (Arial, Times New Roman) during the build to ensure documents look perfect immediately.

    Failure Mode:

        The EULA Trap: The installer pauses for user input ("Accept Yes/No"), breaking the automated headless build script.

        Legal Risk: As developers, accepting a Microsoft EULA on behalf of future users is a potential distribution violation.

✅ Final Choice: The "Grandma-Friendly" Unlocker

    Implementation: We install the Liberation Fonts family (Open Source) by default.

    The "Redmond Lie": We inject a custom XML configuration (30-metric-aliases-ms.conf) into /etc/fonts/conf.d/ so the system substitutes Liberation for Arial seamlessly.

    The Unlocker: We added a friendly question to the Welcome Wizard: "Do you want to install Microsoft Fonts?" If the user clicks "Yes," the system downloads and installs them then, forcing the user to accept the EULA themselves. This is legally clean and user-centric.

🌐 III. The Browser Decision

❌ Initial Choice: Brave Browser

    Concept: A privacy-focused, ad-blocking browser that looks like Chrome.

    Failure Mode:

        Repo Instability: External GPG keys often failed to import inside the chroot build environment, causing "Insecure Memory" errors and build failures.

        Maintenance: Requires constant external repository management.

✅ Final Choice: Mozilla Firefox

    Reasoning:

        Stability: It is the default browser provided by the Ubuntu repositories.

        Updates: Guaranteed security patches for 5 years via Canonical without external repos.

        Privacy: Configured for zero telemetry, aligning with the Lindows ethos.

🛡️ IV. Safety & Partitioning

❌ Initial Choice: Custom "Clean Break" Scripts

    Concept: Shell scripts (remove_windows.sh, Uninstall_Lindows.ps1) to help users wipe their drives easily via the terminal.

    Failure Mode: High Risk. A simple user error or logic bug could wipe the wrong drive or corrupt data. Maintaining these scripts across different Windows versions (8, 10, 11) is unsustainable for a small team.

✅ Final Choice: The "Time Capsule" (Soft Takeover)

    Strategy: Instead of destroying Windows, we make Lindows the "Parasitic Host."

    Implementation:

        Deferred Wiping: We rely on the Calamares Installer to resize Windows safely.

        The Bridge: We created lindows_open_windows.sh and a desktop shortcut "Old Windows Files". This script auto-detects the largest NTFS partition, mounts it securely, and opens it for the user.

        Benefit: No data loss risk. Grandma can still access her old photos without needing to understand "partitions."

🏭 V. The Build Factory

❌ Initial Choice: Cubic (GUI Only)

    Concept: Click-and-drag interface for ISO customization.

    Failure Mode: "Ghost Configs." Cubic caches previous build configurations, leading to bootloader mismatches.

✅ Final Choice: Hybrid Asset Injection

    Implementation: v1_assets.sh

    Mechanism: We wrote a massive Bash script that generates all the custom Python/Shell assets from scratch on the host machine (~/Lindows_Assets_V1) before we even open Cubic.

    The "Human Error" Lesson: During the V0.9 build, a simple directory mismatch (Lindows_Build_V10 vs Lindows_Builds_V10) caused the injection script to target an empty folder. This resulted in a successfully built ISO that contained only a bootloader and no operating system (1GB vs 4GB).

        The Fix: We implemented "Smart Injection" logic (if [ -d ... ]) to verifying the existence of the /etc folder in the target directory before copying assets, ensuring we are injecting into a live OS structure, not a ghost folder.

    Benefit: We get the reliability of Cubic's ISO packing (handling the Xubuntu layers correctly) combined with the precision of a code-based configuration. We inject a fresh, known-good state every time.

🧩 VI. User Experience Features (V1.0 Additions)

    Smart Resolution Wizard: Replaces static resolution changes with a Python tool (resolution_wizard.py) that scans valid hardware resolutions and offers a "Safety Net" (reverts in 15s) to prevent black screens.

    Visual Accessibility: The Welcome Wizard now asks "Do you need larger text?" and instantly scales DPI/Icons if affirmed.

    Desktop Layout: We force the Taskbar to the Bottom and create a custom Red/Gold Start Button to mimic the Windows 98/XP muscle memory.
