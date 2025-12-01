>> 1. CORE_PHILOSOPHY
[TARGET_USER]: "Grandma & Grandpa" (Non-Technical Seniors)
[HARDWARE_TARGET]: Pre-2015 x86_64 (Dual Core / 4GB RAM) UPTO WIN 10 Devices
[ETHOS]:
  - ZERO_TELEMETRY: "If it spies, it dies."
  - MUSCLE_MEMORY: Windows 98/XP aesthetic (Chicago95).
  - DATA_SOVEREIGNTY: Local migration ("Time Capsule") vs Cloud.
  - LEGAL_SAFETY: "Clean IP" (No pre-loaded proprietary fonts/codecs).

>> 2. SYSTEM_ARCHITECTURE (V1.3_STABLE)
[BASE_OS]: Xubuntu 24.04.3 LTS (Noble Numbat)
[KERNEL]: Linux 6.8.0-generic (Hardware Compat > Performance)
[DESKTOP]: XFCE 4.18 | WM: Xfwm4 | DM: LightDM
[THEME]: Chicago95 (Enforced via /etc/xdg/xfce4/xfconf XML injection)
[BOOTLOADER]: GRUB2 (Hybrid MBR/EFI)
  - PATCH: "ignore_uuid" + "cdrom-detect/try-usb=true" (Fixes /dev/sr0 loop)
  - BRANDING: "Try Lindows AU" (Red/Gold Scheme)

>> 3. THE_NUCLEAR_BUILD_PIPELINE (Bash_Automation)
[METHOD]: Headless Cloud Compilation (Cubic GUI Abandoned due to "Ghost Configs")
[INFRASTRUCTURE]: GCP `e2-standard-32` (32vCPU/128GB) | Spot Pricing
[SCRIPT_1]: `v1_3_assets.sh` (The Payload)
  - GENERATES: `~/Lindows_Assets_V1`
  - LOGIC: Clean IP, Auto-Theming, Wallpaper Injection, Script Creation.

>> 4. FEATURE_IMPLEMENTATION_MATRIX
[ACCESSIBILITY]:
  - "Smart Resolution Wizard": Python script scans `xrandr`, offers safe scaling options, auto-reverts in 15s on black screen.
  - "Visibility Mode": Welcome Wizard asks "Do you need larger text?" -> Scripts DPI/Icon scale.
[DATA_BRIDGE]:
  - "Time Capsule": Desktop shortcut (`lindows_open_windows.sh`) auto-mounts largest NTFS partition (C:) via `udisksctl` (No Root Required).
[DRIVERS]:
  - "Driver Assistant": `driver_detect.sh` runs at boot.
  - PRELOADED: `bcmwl-kernel-source` (Broadcom), `nvidia-common`.
  - REMOVED: `firmware-b43-installer` (Checksum Failure/Repo Dead).
[APPS]:
  - BROWSER: Firefox (Replaced Brave due to repo instability).
  - STORE: `gnome-software` (Flatpak Only). Snapd purged.
  - GAMES: Solitaire, Minesweeper (Pinball removed due to video crash).

>> 5. ARTIFACT_STATUS (V1.3)
[ISO]: "Lindows_AU_V1.3.iso" (4.63 GiB)
[HASH]: "Lindows_AU_V1.3.md5" (Verified)
[LOGS]: "building.log"
[STORAGE]: `gs://demo.spers.online/`

>> 6. EVOLUTIONARY_TRAJECTORY (The "War Stories")
[PIVOT_1]: Ubuntu Minimal -> Xubuntu Desktop (Fixed bootloader paths).
[PIVOT_2]: MS Fonts -> Liberation Alias (Fixed EULA build hang).
[PIVOT_3]: "Clean Break" Scripts -> Calamares (Reduced data loss liability).
[PIVOT_4]: Cubic -> Bash (Fixed non-reproducible builds).
[PIVOT_5]: Folder Syntax Error (`Build` vs `Builds`) -> Added "Smart Injector" logic.

>> 7. FUTURE_ROADMAP (Post-Alpha)
[PHASE_2]: "Aesthetic Polish" (Screensaver revival, Custom System Sounds).
[PHASE_3]: "Technical Hardening" (Custom Calamares Partition Module for "2/3rds Rule").
[PHASE_4]: "Distribution" (Whirlpool, Reddit, DankPods outreach).
