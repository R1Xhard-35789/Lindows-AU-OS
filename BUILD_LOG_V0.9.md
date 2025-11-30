# 📐 DESIGN_NOTES.md: Architectural Decisions & Pivots

> **"Stability over Novelty. Privacy over Convenience."**

This document records the critical architectural changes and design decisions made during the Alpha development of **Lindows AU** (V0.0.1 - V0.9). It serves as the historical record of *why* the system is built this way.

---

## 🔄 I. The Base OS Pivot

### **❌ Initial Choice: Ubuntu 24.04 LTS Minimal**
* **Concept:** Start with a bare-bones server image and install only the XFCE desktop to keep the ISO size small (< 2GB).
* **Failure Mode:**
    * **Bootloader "Ghosts":** The Minimal ISO uses a different SquashFS structure (`minimal.squashfs`) than the Desktop installer expects. This caused persistent `/dev/sr0: No medium found` errors on live USBs because GRUB was looking for a server filesystem that didn't exist in the desktop context.
    * **Missing Dependencies:** Critical tools like `add-apt-repository`, `curl`, and `git` were missing, complicating the build script.

### **✅ Final Choice: Xubuntu 24.04 LTS Desktop**
* **Reasoning:**
    * **Native XFCE:** The desktop environment is pre-installed and optimized by the Xubuntu team. This eliminated the need to manually install the desktop, reducing build time and configuration errors.
    * **Standard Boot Path:** It uses the standard `filesystem.squashfs` structure, ensuring 100% compatibility with Rufus, Etcher, and legacy BIOS/UEFI bootloaders without manual hacking.
    * **Efficiency:** While larger (3GB source), it strips down to a very efficient runtime for older hardware.

---

## ⚖️ II. The "Clean IP" Strategy (Legal & EULA)

### **❌ Initial Choice: Microsoft Core Fonts**
* **Concept:** Install `ttf-mscorefonts-installer` (Arial, Times New Roman) to ensure documents look perfect.
* **Failure Mode:**
    * **The EULA Trap:** The installer pauses for user input ("Accept Yes/No"), breaking the automated headless build script.
    * **Legal Risk:** As developers, accepting a Microsoft EULA on behalf of future users is a potential distribution violation.

### **✅ Final Choice: Open Source Aliasing**
* **Implementation:** We install the **Liberation Fonts** family (Open Source).
* **The "Redmond Lie":** We inject a custom XML configuration (`30-metric-aliases-ms.conf`) into `/etc/fonts/conf.d/`.
* **Result:** When an application asks for "Times New Roman," the system silently hands it "Liberation Serif." The document looks identical (same metrics), but the OS remains **100% FOSS with Zero EULAs**.

---

## 🌐 III. The Browser Decision

### **❌ Initial Choice: Brave Browser**
* **Concept:** A privacy-focused, ad-blocking browser that looks like Chrome.
* **Failure Mode:**
    * **Repo Instability:** External GPG keys often failed to import inside the `chroot` build environment, causing "Insecure Memory" errors and build failures.
    * **Maintenance:** Requires constant external repository management.

### **✅ Final Choice: Mozilla Firefox**
* **Reasoning:**
    * **Stability:** It is the default browser provided by the Ubuntu repositories.
    * **Updates:** Guaranteed security patches for 5 years via Canonical without external repos.
    * **Privacy:** Configured for zero telemetry, aligning with the Lindows ethos.

---

## 🛡️ IV. Safety & Partitioning

### **❌ Initial Choice: Custom "Clean Break" Scripts**
* **Concept:** Shell scripts (`remove_windows.sh`, `Uninstall_Lindows.ps1`) to help users wipe their drives easily via the terminal.
* **Failure Mode:** **High Risk.** A simple user error or logic bug could wipe the wrong drive or corrupt data. Maintaining these scripts across different Windows versions (8, 10, 11) is unsustainable for a small team.

### **✅ Final Choice: Deferred Automation**
* **Action:** These scripts were **deleted** from the repository (V0.2).
* **Strategy:** We currently rely on the **Calamares Installer**'s native, tested partition manager.
* **Future:** Phase 3 will introduce a custom Python module for Calamares to handle "Nuke & Pave" operations safely within the GUI.

---

## 🏭 V. The Build Factory

### **❌ Initial Choice: Cubic (GUI)**
* **Concept:** A graphical wizard for ISO customization.
* **Failure Mode:** **"Ghost Configs."** Cubic caches previous build configurations, leading to bootloader mismatches (e.g., looking for `minimal.standard.squashfs` after switching to Xubuntu) which broke the boot process.

### **✅ Final Choice: Headless Bash Script**
* **Implementation:** `build_lindows_final.sh` (The "Nuclear" Option).
* **Mechanism:** A script that deletes the entire build directory and reconstructs the OS layer-by-layer (extracting and merging the 3 Xubuntu squashfs layers) every time.
* **Benefit:** Guarantees a clean, reproducible build with zero "ghosts" from previous attempts.
* **Bootloader Rescue:** Includes logic to extract the hidden `efi.img` from the source ISO using `xorriso`, preventing boot failures on UEFI systems.


### **NOTES** 

SYSLINK PATCH

#!/bin/bash
# LINDOWS AU - V0.9 "GHOST LAYER" PATCH
# Fixes "File system layers are missing" by aliasing the old names to the new file.

set -e
WORK_DIR="$HOME/Lindows_Factory_Final"
OUTPUT_ISO="Lindows_AU_v0.9.iso"

echo "--- 👻 CREATING GHOST LAYERS ---"

cd "$WORK_DIR/extract/casper"

# Create symlinks so Casper finds "what it expects" but loads our file
# We point ALL expected layer names to our unified 'filesystem.squashfs'
ln -sf filesystem.squashfs minimal.squashfs
ln -sf filesystem.squashfs minimal.standard.squashfs
ln -sf filesystem.squashfs minimal.standard.live.squashfs

echo "Symlinks created. Casper will now accept the unified file."

# --- REPACK ISO ---
echo "--- 💿 GENERATING V0.9 ISO ---"
cd "$WORK_DIR/extract"
rm md5sum.txt 2>/dev/null
find . -type f -print0 | xargs -0 md5sum | grep -v isolinux/boot.cat | sudo tee md5sum.txt

xorriso -as mkisofs \
  -r -V "LINDOWS_AU_V0_9" \
  -J -joliet-long -l \
  -iso-level 3 \
  -partition_offset 16 \
  -b boot/grub/i386-pc/eltorito.img \
     -no-emul-boot -boot-load-size 4 -boot-info-table --grub2-boot-info \
  -eltorito-alt-boot \
  -e boot/grub/efi.img \
     -no-emul-boot -isohybrid-gpt-basdat \
  -o "$HOME/$OUTPUT_ISO" \
  .

echo "--- ✅ V0.9 COMPLETE: $HOME/$OUTPUT_ISO ---"


v0.8 BUILD SCRIPT

#!/bin/bash
# LINDOWS AU - NUCLEAR FACTORY BUILDER (V1.2 - BRANDED)
# Changes: Rebrands GRUB menu to "Lindows AU"

set -e

# --- CONFIGURATION ---
WORK_DIR="$HOME/Lindows_Factory_Final"
ASSETS_SRC="/home/change4caring/Lindows_Assets"
ISO_FILENAME="xubuntu-24.04.iso"
OUTPUT_ISO="Lindows_AU_v0.8.iso"

# --- 1. CLEANUP ---
echo "--- 🧹 CLEARING FACTORY FLOOR ---"
sudo umount "$WORK_DIR/staging/sys" 2>/dev/null || true
sudo umount "$WORK_DIR/staging/proc" 2>/dev/null || true
sudo umount "$WORK_DIR/staging/dev" 2>/dev/null || true
sudo umount "$WORK_DIR/mnt" 2>/dev/null || true
sudo rm -rf "$WORK_DIR"
mkdir -p "$WORK_DIR/mnt" "$WORK_DIR/extract" "$WORK_DIR/staging"

# --- 2. MOUNT ISO ---
echo "--- 📦 MOUNTING ISO ---"
sudo mount -o loop "$HOME/Downloads/$ISO_FILENAME" "$WORK_DIR/mnt"

# --- 3. COPY BASE ISO STRUCTURE ---
echo "--- 🚚 COPYING ISO BOOT FILES ---"
rsync -a --exclude=/casper/*.squashfs "$WORK_DIR/mnt/" "$WORK_DIR/extract"

# --- 4. LAYER CAKE EXTRACTION ---
echo "--- 🍰 STACKING FILESYSTEM LAYERS ---"
sudo unsquashfs -f -d "$WORK_DIR/staging" "$WORK_DIR/mnt/casper/minimal.squashfs"
sudo unsquashfs -f -d "$WORK_DIR/staging" "$WORK_DIR/mnt/casper/minimal.standard.squashfs"
sudo unsquashfs -f -d "$WORK_DIR/staging" "$WORK_DIR/mnt/casper/minimal.standard.live.squashfs"
sudo umount "$WORK_DIR/mnt"

# --- 5. INJECTION ---
echo "--- 💉 PREPARING CHROOT ---"
sudo mkdir -p "$WORK_DIR/staging/dev" "$WORK_DIR/staging/proc" "$WORK_DIR/staging/sys" "$WORK_DIR/staging/etc" "$WORK_DIR/staging/opt/assets" "$WORK_DIR/staging/tmp"
sudo cp -r "$ASSETS_SRC"/* "$WORK_DIR/staging/opt/assets/"
sudo chmod -R 777 "$WORK_DIR/staging/opt/assets"
sudo cp /etc/resolv.conf "$WORK_DIR/staging/etc/"
sudo mount --bind /dev "$WORK_DIR/staging/dev"
sudo mount -t proc proc "$WORK_DIR/staging/proc"
sudo mount -t sysfs sysfs "$WORK_DIR/staging/sys"

# --- 6. EXECUTION ---
echo "--- ⚙️ RUNNING MASTER INSTALL SCRIPT ---"
sudo chroot "$WORK_DIR/staging" /bin/bash -c "bash /opt/assets/install_core.sh"

# --- 7. CLEANUP CHROOT ---
echo "--- 🧼 CLEANING UP ---"
sudo umount "$WORK_DIR/staging/sys"
sudo umount "$WORK_DIR/staging/proc"
sudo umount "$WORK_DIR/staging/dev"
sudo rm "$WORK_DIR/staging/etc/resolv.conf"

# --- 8. BOOTLOADER PATCHING (BRANDING & FIXES) ---
echo "--- 🔧 PATCHING GRUB ---"
CONFIG_DIR="$WORK_DIR/extract/boot/grub"

# A. Fix the "No Medium" Error (Force filesystem name)
sudo sed -i 's/minimal.standard.live.squashfs/filesystem.squashfs/g' "$CONFIG_DIR/grub.cfg"
sudo sed -i 's/minimal.standard.squashfs/filesystem.squashfs/g' "$CONFIG_DIR/grub.cfg"
sudo sed -i 's/minimal.squashfs/filesystem.squashfs/g' "$CONFIG_DIR/grub.cfg"

# B. Fix Hardware Compatibility (Nomodeset)
sudo sed -i 's/quiet splash/quiet splash nomodeset/g' "$CONFIG_DIR/grub.cfg"

# C. BRANDING (The "Lindows" text)
# Replaces "Try or Install Xubuntu" with "Try Lindows AU"
sudo sed -i 's/Try or Install Xubuntu/Try Lindows AU/g' "$CONFIG_DIR/grub.cfg"
sudo sed -i 's/Xubuntu/Lindows AU/g' "$CONFIG_DIR/grub.cfg"

# --- 9. REPACKAGING ---
echo "--- 📦 COMPRESSING UNIFIED FILESYSTEM (zstd) ---"
sudo mksquashfs "$WORK_DIR/staging" "$WORK_DIR/extract/casper/filesystem.squashfs" -comp zstd -Xcompression-level 15 -noappend
printf $(du -sx --block-size=1 "$WORK_DIR/staging" | cut -f1) > "$WORK_DIR/extract/casper/filesystem.size"

# --- 10. BOOT IMAGE RECOVERY (From V0.7 Lesson) ---
echo "--- 🔧 RECOVERING BOOT IMAGE ---"
# We extract the hidden EFI image again to ensure the new build has it
xorriso -osirrox on -indev "$HOME/Downloads/$ISO_FILENAME" -extract_boot_images "$WORK_DIR/extract/boot/grub"
EFI_FOUND=$(find "$WORK_DIR/extract/boot/grub" -name "*efi.img*" | head -n 1)
if [ -z "$EFI_FOUND" ]; then EFI_FOUND=$(find "$WORK_DIR/extract/boot/grub" -size +1M -size -10M -name "*.img" | head -n 1); fi
mv "$EFI_FOUND" "$WORK_DIR/extract/boot/grub/efi.img"

# --- 11. GENERATE ISO ---
echo "--- 💿 BUILDING V0.8 ISO ---"
cd "$WORK_DIR/extract"
find . -type f -print0 | xargs -0 md5sum | grep -v isolinux/boot.cat | sudo tee md5sum.txt

xorriso -as mkisofs \
  -r -V "LINDOWS_AU_V0_8" \
  -J -joliet-long -l \
  -iso-level 3 \
  -partition_offset 16 \
  -b boot/grub/i386-pc/eltorito.img \
     -no-emul-boot -boot-load-size 4 -boot-info-table --grub2-boot-info \
  -eltorito-alt-boot \
  -e boot/grub/efi.img \
     -no-emul-boot -isohybrid-gpt-basdat \
  -o "$HOME/$OUTPUT_ISO" \
  .

echo "--- ✅ DONE: $HOME/$OUTPUT_ISO ---"
