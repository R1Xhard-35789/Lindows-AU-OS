# **☢️ Lindows AU V1.1: The Nuclear Build Protocol**

**"Total Automation. Zero Ghosts. Maximum Stability."**

Version 1.1 represents the final "Gold Master" architecture for the Lindows AU Alpha. It abandons graphical tools (Cubic) in favor of a purely code-driven pipeline to ensure 100% reproducibility and eliminate "ghost configuration" errors.

---

## **🏗️ Phase 1: Asset Generation (The Payload)**

We do not edit files inside the ISO manually. Instead, we run a "Generator Script" on the host machine that creates a clean, perfect copy of every custom script, icon, and configuration file we need.

### **Script: `v1_1_assets.sh`**

* **Purpose:** Generates the `~/Lindows_Assets_V1` folder.  
* **Includes:**  
  * **Smart Resolution Wizard:** Python tool for safe DPI scaling.  
  * **Time Capsule:** Auto-mounting script for the old Windows partition.  
  * **Desktop Architect:** Generates the "Red/Gold" Start Button and forces the bottom panel layout.  
  * **Clean IP Logic:** Handling for Microsoft Fonts and EULAs.

Bash

\#\!/bin/bash  
\# LINDOWS AU \- V1.1 ASSET GENERATION (POLISHED)  
\# Changelog:  
\# \- Fixed "Untrusted Desktop Launcher" errors via Welcome Wizard  
\# \- Forced Panel to Bottom (Windows Style)  
\# \- Enhanced Red/Gold Start Button  
\# \- Added "Time Capsule" & "Smart Resolution"

set \-e

\# 1\. Clean Slate  
ASSETS\_DIR="$HOME/Lindows\_Assets\_V1"  
rm \-rf "$ASSETS\_DIR"  
mkdir \-p "$ASSETS\_DIR"

echo "--- 🏭 GENERATING V1.1 ASSETS \---"

\# \--- SCRIPT 1: MASTER INJECTOR \---  
cat \<\<EOF \> "$ASSETS\_DIR/install\_core.sh"  
\#\!/bin/bash  
\# LINDOWS AU \- CORE INSTALLER (V1.1)

set \-e  
echo "--- STARTING CORE INJECTION \---"

\# A. PREP  
apt update  
apt install \-y curl wget nano software-properties-common x11-xserver-utils git

\# B. INSTALL CORE TOOLS  
apt install \-y calamares zenity flatpak gnome-software-plugin-flatpak \\  
    vlc mpv thunar mousepad \\  
    pciutils usbutils lshw imagemagick \\  
    aisleriot gnome-mines \\  
    ubuntu-restricted-addons gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly libavcodec-extra unrar \\  
    fonts-liberation fonts-noto

\# Attempt to install pinball  
apt install \-y pinball || echo "Pinball package not found, skipping."

\# C. CACHE MICROSOFT FONTS  
mkdir \-p /opt/lindows/cache  
cd /opt/lindows/cache  
apt download ttf-mscorefonts-installer 2\>/dev/null || echo "Font download skipped"

\# D. DEPLOY SCRIPTS  
cp /opt/assets/lindows\_welcome.py /usr/local/bin/  
cp /opt/assets/driver\_detect.sh /usr/local/bin/  
cp /opt/assets/run\_driver\_assistant.py /usr/local/bin/  
cp /opt/assets/install\_ms\_fonts.sh /usr/local/bin/  
cp /opt/assets/resolution\_wizard.py /usr/local/bin/  
cp /opt/assets/lindows\_open\_windows.sh /usr/local/bin/  
chmod \+x /usr/local/bin/\*.sh /usr/local/bin/\*.py

\# E. SUB-ROUTINES  
bash /opt/assets/lockdown\_store.sh  
bash /opt/assets/build\_desktop.sh

\# F. AUTOSTART  
mkdir \-p /etc/skel/.config/autostart  
cat \<\<E \> /etc/skel/.config/autostart/lindows-welcome.desktop  
\[Desktop Entry\]  
Type=Application  
Name=Welcome Wizard  
Exec=/usr/local/bin/lindows\_welcome.py  
Hidden=false  
NoDisplay=false  
X-GNOME-Autostart-enabled=true  
E

\# G. THEME (Chicago95)  
echo "Installing Theme..."  
cd /tmp  
rm \-rf Chicago95  
git clone \--depth 1 https://github.com/grassmunk/Chicago95.git  
mkdir \-p /usr/share/themes /usr/share/icons  
cp \-r Chicago95/Theme/Chicago95 /usr/share/themes/  
cp \-r Chicago95/Icons/\* /usr/share/icons/

\# H. CLEANUP  
apt autoremove \-y  
apt clean  
rm \-rf /tmp/\*

echo "--- INJECTION COMPLETE \---"  
EOF

\# \--- SCRIPT 2: WELCOME WIZARD (Now Fixes "Untrusted" Icons) \---  
cat \<\<'EOF' \> "$ASSETS\_DIR/lindows\_welcome.py"  
\#\!/usr/bin/env python3  
import subprocess, os, sys, glob

def zenity(args):  
    return subprocess.run(\['zenity'\] \+ args, stdout=subprocess.PIPE, text=True).returncode \== 0

def trust\_desktop\_icons():  
    \# This fixes the "Untrusted Application Launcher" error  
    try:  
        desktop \= os.path.expanduser("\~/Desktop")  
        for root, dirs, files in os.walk(desktop):  
            for file in files:  
                if file.endswith(".desktop"):  
                    full\_path \= os.path.join(root, file)  
                    os.chmod(full\_path, 0o755)  
                    subprocess.run(\['gio', 'set', full\_path, 'metadata::trusted', 'yes'\])  
    except:  
        pass

def main():  
    \# 0\. Silent Fixes  
    trust\_desktop\_icons()

    \# 1\. Intro  
    zenity(\['--info',   
            '--title=Welcome to Lindows AU',   
            '--text=\<b\>Welcome\!\</b\>\\n\\nYour system is ready. We prioritize simplicity and privacy.',   
            '--width=450',   
            '--icon-name=computer'\])  
      
    \# 2\. Smart Resolution Check  
    want\_setup \= subprocess.call(\[  
        'zenity', '--question',  
        '--title=Screen Setup',  
        '--text=\<b\>Adjust Screen Size?\</b\>\\n\\nDo you want to adjust the text size and screen resolution now?',  
        '--ok-label=Yes, Adjust',  
        '--cancel-label=No, It looks good'  
    \]) \== 0

    if want\_setup:  
        subprocess.call(\['/usr/local/bin/resolution\_wizard.py'\])

    \# 3\. Font Unlocker  
    want\_fonts \= subprocess.call(\[  
        'zenity', '--question',  
        '--title=Improve Documents?',  
        '--text=\<b\>Install Microsoft Fonts?\</b\>\\n\\nTo make Word documents look perfect, we can install Arial and Times New Roman.\\n\\nThis requires agreeing to a Microsoft license.',  
        '--ok-label=Install Fonts',  
        '--cancel-label=Skip'  
    \]) \== 0

    if want\_fonts:  
        subprocess.Popen(\['/usr/local/bin/install\_ms\_fonts.sh'\])

    \# 4\. Ready  
    zenity(\['--info',   
            '--title=Setup Complete',   
            '--text=\<b\>System Ready\!\</b\>\\n\\nYour computer is fully configured.',   
            '--width=450'\])

    \# 5\. Self-Destruct  
    try:  
        os.remove(os.path.expanduser("\~/.config/autostart/lindows-welcome.desktop"))  
    except:  
        pass

if \_\_name\_\_ \== "\_\_main\_\_":  
    main()  
EOF

\# \--- SCRIPT 3: SMART RESOLUTION WIZARD \---  
cat \<\<'EOF' \> "$ASSETS\_DIR/resolution\_wizard.py"  
\#\!/usr/bin/env python3  
import subprocess  
import re  
import sys

def zenity(args):  
    cmd \= \['zenity'\] \+ args  
    try:  
        res \= subprocess.run(cmd, stdout=subprocess.PIPE, stderr=subprocess.PIPE, text=True)  
        return res.returncode \== 0, res.stdout.strip()  
    except:  
        return False, ""

def get\_screen\_info():  
    try:  
        output \= subprocess.check\_output(\['xrandr'\]).decode('utf-8')  
        connected\_match \= re.search(r'^(\\S+) connected', output, re.MULTILINE)  
        if not connected\_match: return None, \[\], None  
        display\_name \= connected\_match.group(1)  
        resolutions \= \[\]  
        current\_res \= None  
        for line in output.split('\\n'):  
            match \= re.search(r'^\\s+(\\d+)x(\\d+)', line)  
            if match:  
                w, h \= int(match.group(1)), int(match.group(2))  
                res\_str \= f"{w}x{h}"  
                resolutions.append({'w': w, 'h': h, 'str': res\_str})  
                if '\*' in line: current\_res \= res\_str  
        return display\_name, resolutions, current\_res  
    except:  
        return None, \[\], None

def apply\_res(display, res\_str):  
    subprocess.run(\['xrandr', '--output', display, '--mode', res\_str\])

def main():  
    display, resolutions, current \= get\_screen\_info()  
    if not display: return  
      
    resolutions.sort(key=lambda x: x\['w'\], reverse=True)  
    try: curr\_idx \= next(i for i, r in enumerate(resolutions) if r\['str'\] \== current)  
    except: curr\_idx \= 0

    options \= \[\]  
    options.append(current)  
    options.append("Current (Standard)")  
      
    labels \= \["Large Text", "Huge Text", "Giant Text"\]  
    label\_idx \= 0  
    for i in range(curr\_idx \+ 1, min(len(resolutions), curr\_idx \+ 4)):  
        options.append(resolutions\[i\]\['str'\])  
        options.append(labels\[label\_idx\])  
        label\_idx \+= 1

    success, selection \= zenity(\[  
        '--list', '--title=Screen Size',   
        '--text=\<b\>Choose a size.\</b\>\\nLower resolution makes text BIGGER.\\n\\n\<i\>Screen will revert in 15s if not confirmed.\</i\>',  
        '--column=Resolution', '--column=Description',  
        '--height=400', '--width=450', '--print-column=1'  
    \] \+ options)

    if success and selection and selection \!= current:  
        apply\_res(display, selection)  
        for i in range(15, 0, \-1):  
            rc \= subprocess.call(\[  
                'zenity', '--question',   
                '--text=\<b\>Is this better?\</b\>\\n\\nClick OK to keep.\\nReverting in {}s...'.format(i),  
                '--ok-label=Keep It', '--cancel-label=Revert', '--timeout=1'  
            \])  
            if rc \== 0: return  
            if rc \== 1: break  
          
        apply\_res(display, current)  
        zenity(\['--info', '--text=Restored previous settings.'\])

if \_\_name\_\_ \== "\_\_main\_\_":  
    main()  
EOF

\# \--- SCRIPT 4: TIME CAPSULE \---  
cat \<\<'EOF' \> "$ASSETS\_DIR/lindows\_open\_windows.sh"  
\#\!/bin/bash  
\# LINDOWS AU \- TIME CAPSULE  
WIN\_PART=$(lsblk \-lnb \-o NAME,SIZE,FSTYPE | grep ntfs | sort \-k2 \-nr | head \-n1 | awk '{print $1}')  
if \[ \-z "$WIN\_PART" \]; then  
    zenity \--error \--text="No Windows files found on this computer." \--width=300  
    exit 1  
fi  
udisksctl mount \-b "/dev/$WIN\_PART" \> /tmp/mount\_out 2\>&1  
MOUNT\_POINT=$(findmnt \-rn \-o TARGET \-S "/dev/$WIN\_PART")  
if \[ \-z "$MOUNT\_POINT" \]; then  
    MOUNT\_POINT=$(cat /tmp/mount\_out | awk '{print $NF}' | tr \-d '.')  
fi  
if \[ \-n "$MOUNT\_POINT" \]; then  
    thunar "$MOUNT\_POINT"  
else  
    zenity \--error \--text="Could not open the drive."  
fi  
EOF

\# \--- SCRIPT 5: FONT BACKEND \---  
cat \<\<EOF \> "$ASSETS\_DIR/install\_ms\_fonts.sh"  
\#\!/bin/bash  
(  
echo "10"; echo "\# Preparing..."  
echo ttf-mscorefonts-installer msttcorefonts/accepted-mscorefonts-eula select true | debconf-set-selections  
echo "50"; echo "\# Installing Fonts..."  
if \[ \-f /opt/lindows/cache/ttf-mscorefonts-installer\*.deb \]; then  
    dpkg \-i /opt/lindows/cache/ttf-mscorefonts-installer\*.deb || apt-get install \-f \-y  
else  
    apt-get install \-y ttf-mscorefonts-installer  
fi  
echo "100"; echo "\# Done."  
) | zenity \--progress \--title="Installing Fonts" \--auto-close  
EOF

\# \--- SCRIPT 6: DESKTOP ARCHITECT \---  
cat \<\<EOF \> "$ASSETS\_DIR/build\_desktop.sh"  
\#\!/bin/bash  
SKEL="/etc/skel/Desktop"  
GAMES="\\$SKEL/Games"  
APPS="\\$SKEL/Essential Apps"  
ICONS="/usr/share/icons/lindows"

mkdir \-p "\\$SKEL" "\\$GAMES" "\\$APPS" "\\$ICONS"

\# 1\. GENERATE RED LINDOWS BUTTON  
if command \-v convert \>/dev/null; then  
    convert \-size 100x35 xc:firebrick \-fill gold \-font Liberation-Sans-Bold \\  
            \-pointsize 20 \-gravity center \-draw "text 0,0 'Lindows'" \\  
            "\\$ICONS/start\_button.png"  
else  
    touch "\\$ICONS/start\_button.png"  
fi

\# 2\. MOVE PANEL TO BOTTOM (Forced)  
mkdir \-p /etc/xdg/xfce4/xfconf/xfce-perchannel-xml/  
cat \<\<E \> /etc/xdg/xfce4/xfconf/xfce-perchannel-xml/xfce4-panel.xml  
\<?xml version="1.0" encoding="UTF-8"?\>  
\<channel name="xfce4-panel" version="1.0"\>  
  \<property name="panels" type="uint" value="1"\>  
    \<property name="panel-1" type="empty"\>  
      \<property name="position" type="string" value="p=12;x=0;y=0"/\>  
      \<property name="length" type="uint" value="100"/\>  
      \<property name="position-locked" type="bool" value="true"/\>  
      \<property name="size" type="uint" value="36"/\>  
      \<property name="plugin-ids" type="array"\>  
        \<value type="int" value="1"/\>  
        \<value type="int" value="2"/\>  
        \<value type="int" value="3"/\>  
        \<value type="int" value="4"/\>  
      \</property\>  
    \</property\>  
  \</property\>  
  \<property name="plugins" type="empty"\>  
    \<property name="plugin-1" type="string" value="whiskermenu"/\>  
    \<property name="plugin-2" type="string" value="tasklist"/\>  
    \<property name="plugin-3" type="string" value="separator"\>  
      \<property name="expand" type="bool" value="true"/\>  
      \<property name="style" type="uint" value="0"/\>  
    \</property\>  
    \<property name="plugin-4" type="string" value="clock"/\>  
  \</property\>  
\</channel\>  
E

\# 3\. INSTALLER ICON  
rm \-f "\\$SKEL/ubiquity.desktop" "\\$SKEL/install-xubuntu.desktop"  
cat \<\<E \> "\\$SKEL/Install Lindows AU.desktop"  
\[Desktop Entry\]  
Version=1.0  
Type=Application  
Name=Install Lindows AU  
Exec=calamares  
Icon=system-software-install  
Terminal=false  
E

\# 4\. TIME CAPSULE SHORTCUT  
cat \<\<E \> "\\$SKEL/Old Windows Files.desktop"  
\[Desktop Entry\]  
Version=1.0  
Type=Application  
Name=Old Windows Files  
Comment=Access your old C: Drive  
Exec=/usr/local/bin/lindows\_open\_windows.sh  
Icon=drive-harddisk  
Terminal=false  
E

\# 5\. GAME SHORTCUTS  
cat \<\<E \> "\\$GAMES/Solitaire.desktop"  
\[Desktop Entry\]  
Version=1.0  
Type=Application  
Name=Solitaire  
Exec=sol  
Icon=aisleriot  
Terminal=false  
E

cat \<\<E \> "\\$GAMES/Minesweeper.desktop"  
\[Desktop Entry\]  
Version=1.0  
Type=Application  
Name=Minesweeper  
Exec=gnome-mines  
Icon=gnome-mines  
Terminal=false  
E

cat \<\<E \> "\\$GAMES/Pinball.desktop"  
\[Desktop Entry\]  
Version=1.0  
Type=Application  
Name=3D Pinball  
Exec=pinball  
Icon=pinball  
Terminal=false  
E

\# 6\. PERMISSIONS  
chmod \+x "\\$SKEL/"\*.desktop  
chmod \+x "\\$GAMES/"\*.desktop  
EOF

\# \--- SCRIPT 7: DRIVER DETECT \---  
cat \<\<EOF \> "$ASSETS\_DIR/driver\_detect.sh"  
\#\!/bin/bash  
\[ \-f "\\$HOME/Desktop/Hardware Assistant.desktop" \] && exit 0  
if lspci \-nnk | grep \-q '14e4:43\[c1-c9\]'; then  
    cat \<\<E \> "\\$HOME/Desktop/Hardware Assistant.desktop"  
\[Desktop Entry\]  
Version=1.0  
Type=Application  
Name=Hardware Assistant  
Exec=/usr/local/bin/run\_driver\_assistant.py  
Icon=gnome-settings-drivers  
Terminal=false  
E  
    chmod \+x "\\$HOME/Desktop/Hardware Assistant.desktop"  
fi  
EOF

\# \--- SCRIPT 8: DRIVER UI \---  
cat \<\<EOF \> "$ASSETS\_DIR/run\_driver\_assistant.py"  
\#\!/usr/bin/env python3  
import subprocess  
def main():  
    subprocess.run(\['zenity', '--info', '--text', 'Hardware drivers detected. Install via Synaptic.'\], timeout=2)  
if \_\_name\_\_ \== "\_\_main\_\_":  
    main()  
EOF

\# \--- SCRIPT 9: STORE LOCKDOWN \---  
cat \<\<EOF \> "$ASSETS\_DIR/lockdown\_store.sh"  
\#\!/bin/bash  
apt-get purge \-y snapd snap-store  
apt-mark hold snapd  
apt-get install \-y gnome-software gnome-software-plugin-flatpak flatpak  
apt-get remove \-y gnome-software-plugin-deb packagekit  
flatpak remote-add \--if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo  
sed \-i 's/Exec=snap-store/Exec=gnome-software/' /usr/share/applications/org.gnome.Software.desktop 2\>/dev/null  
EOF

echo "--- V1.1 ASSETS READY IN: $ASSETS\_DIR \---"

**☢️ Phase 2: The Nuclear Factory (The Builder)**

We use a headless script to dismantle the Xubuntu ISO, inject our assets, fix the bootloader, and repack it.

### **Script: `build_nuclear_v11.sh`**

* **Usage:** `sudo bash build_nuclear_v11.sh`  
* **Key Feature:** **The Layer Stack.** It extracts `minimal` \+ `standard` \+ `live` and merges them into one filesystem, solving the missing `chroot` dependencies.  
* **Boot Fix:** It applies `ignore_uuid` and `cdrom-detect/try-usb=true` to `grub.cfg` and creates symlinks for the old layer names.

Bash  
\#\!/bin/bash  
\# LINDOWS AU \- NUCLEAR FACTORY BUILDER V1.1 (FINAL)  
\# Replaces Cubic. Fully automated "Layer Cake" build.

set \-e

\# \--- CONFIGURATION \---  
WORK\_DIR="$HOME/Lindows\_Factory\_Nuclear"  
ASSETS\_SRC="$HOME/Lindows\_Assets\_V1"  
ISO\_SOURCE="$HOME/Downloads/xubuntu-24.04.iso"  
OUTPUT\_ISO="Lindows\_AU\_V1.1.iso"

\# \--- 1\. CLEANUP \---  
echo "--- 🧹 CLEARING FACTORY FLOOR \---"  
\# Unmount potential leftovers  
sudo umount "$WORK\_DIR/staging/sys" 2\>/dev/null || true  
sudo umount "$WORK\_DIR/staging/proc" 2\>/dev/null || true  
sudo umount "$WORK\_DIR/staging/dev" 2\>/dev/null || true  
sudo umount "$WORK\_DIR/mnt" 2\>/dev/null || true  
sudo rm \-rf "$WORK\_DIR"  
mkdir \-p "$WORK\_DIR/mnt" "$WORK\_DIR/extract" "$WORK\_DIR/staging"

\# \--- 2\. MOUNT ISO \---  
echo "--- 📦 MOUNTING SOURCE ISO \---"  
if \[ \! \-f "$ISO\_SOURCE" \]; then  
    echo "❌ ERROR: Source ISO not found at $ISO\_SOURCE"  
    exit 1  
fi  
sudo mount \-o loop "$ISO\_SOURCE" "$WORK\_DIR/mnt"

\# \--- 3\. EXTRACT BOOTLOADER & BASE \---  
echo "--- 🚚 COPYING BOOT FILES \---"  
rsync \-a \--exclude=/casper/\*.squashfs "$WORK\_DIR/mnt/" "$WORK\_DIR/extract"

\# \--- 4\. LAYER CAKE MERGE (The "Red Bridge") \---  
echo "--- 🍰 STACKING FILESYSTEM LAYERS \---"  
\# We extract them in order (Bottom \-\> Top) to merge them into one  
sudo unsquashfs \-f \-d "$WORK\_DIR/staging" "$WORK\_DIR/mnt/casper/minimal.squashfs"  
sudo unsquashfs \-f \-d "$WORK\_DIR/staging" "$WORK\_DIR/mnt/casper/minimal.standard.squashfs"  
sudo unsquashfs \-f \-d "$WORK\_DIR/staging" "$WORK\_DIR/mnt/casper/minimal.standard.live.squashfs"

sudo umount "$WORK\_DIR/mnt"

\# \--- 5\. INJECTION \---  
echo "--- 💉 INJECTING V1.1 ASSETS \---"  
sudo mkdir \-p "$WORK\_DIR/staging/opt/assets"  
sudo cp \-r "$ASSETS\_SRC"/\* "$WORK\_DIR/staging/opt/assets/"  
sudo chmod \-R 777 "$WORK\_DIR/staging/opt/assets"

\# \--- 6\. CHROOT EXECUTION \---  
echo "--- ⚙️ RUNNING INSTALLER \---"  
\# Bind mounts for networking/updates  
sudo cp /etc/resolv.conf "$WORK\_DIR/staging/etc/"  
sudo mount \--bind /dev "$WORK\_DIR/staging/dev"  
sudo mount \-t proc proc "$WORK\_DIR/staging/proc"  
sudo mount \-t sysfs sysfs "$WORK\_DIR/staging/sys"

\# Run the core installer (Installs Games, Apps, Themes)  
sudo chroot "$WORK\_DIR/staging" /bin/bash \-c "bash /opt/assets/install\_core.sh"

\# Cleanup mounts  
sudo umount "$WORK\_DIR/staging/sys"  
sudo umount "$WORK\_DIR/staging/proc"  
sudo umount "$WORK\_DIR/staging/dev"  
sudo rm "$WORK\_DIR/staging/etc/resolv.conf"

\# \--- 7\. BOOTLOADER PATCHING \---  
echo "--- 🔧 PATCHING GRUB \---"  
CONFIG\_DIR="$WORK\_DIR/extract/boot/grub"

\# A. Branding  
sudo sed \-i 's/Try or Install Xubuntu/Try Lindows AU/g' "$CONFIG\_DIR/grub.cfg"  
sudo sed \-i 's/Xubuntu/Lindows AU/g' "$CONFIG\_DIR/grub.cfg"

\# B. "No Medium" Fix (Point to specific file \+ Ignore UUID)  
sudo sed \-i 's/minimal.standard.live.squashfs/filesystem.squashfs/g' "$CONFIG\_DIR/grub.cfg"  
sudo sed \-i 's/minimal.standard.squashfs/filesystem.squashfs/g' "$CONFIG\_DIR/grub.cfg"  
sudo sed \-i 's/minimal.squashfs/filesystem.squashfs/g' "$CONFIG\_DIR/grub.cfg"  
\# Inject robust boot flags  
sudo sed \-i 's/quiet splash/quiet splash nomodeset ignore\_uuid cdrom-detect\\/try-usb=true/g' "$CONFIG\_DIR/grub.cfg"

\# \--- 8\. THE GHOST PATCH \---  
echo "--- 👻 CREATING GHOST SYMLINKS \---"  
\# Trick Casper into thinking the old layers still exist  
cd "$WORK\_DIR/extract/casper"  
\# Note: We haven't created filesystem.squashfs yet, but we link to it now  
sudo ln \-sf filesystem.squashfs minimal.squashfs  
sudo ln \-sf filesystem.squashfs minimal.standard.squashfs  
sudo ln \-sf filesystem.squashfs minimal.standard.live.squashfs  
cd "$HOME"

\# \--- 9\. REPACKAGING \---  
echo "--- 📦 COMPRESSING OS (ZSTD) \---"  
\# This creates the ONE big file that contains everything  
sudo mksquashfs "$WORK\_DIR/staging" "$WORK\_DIR/extract/casper/filesystem.squashfs" \-comp zstd \-Xcompression-level 15 \-noappend  
\# Update size file  
printf $(du \-sx \--block-size=1 "$WORK\_DIR/staging" | cut \-f1) \> "$WORK\_DIR/extract/casper/filesystem.size"

\# \--- 10\. BOOT IMAGE RECOVERY \---  
echo "--- 🔌 RECOVERING EFI IMAGE \---"  
xorriso \-osirrox on \-indev "$ISO\_SOURCE" \-extract\_boot\_images "$WORK\_DIR/extract/boot/grub"  
EFI\_FOUND=$(find "$WORK\_DIR/extract/boot/grub" \-name "\*efi.img\*" | head \-n 1\)  
if \[ \-z "$EFI\_FOUND" \]; then EFI\_FOUND=$(find "$WORK\_DIR/extract/boot/grub" \-size \+1M \-size \-10M \-name "\*.img" | head \-n 1); fi  
mv "$EFI\_FOUND" "$WORK\_DIR/extract/boot/grub/efi.img"

\# \--- 11\. GENERATE ISO \---  
echo "--- 💿 BURNING ISO \---"  
cd "$WORK\_DIR/extract"  
find . \-type f \-print0 | xargs \-0 md5sum | grep \-v isolinux/boot.cat | sudo tee md5sum.txt

xorriso \-as mkisofs \\  
  \-r \-V "LINDOWS\_AU\_V1\_1" \\  
  \-J \-joliet-long \-l \\  
  \-iso-level 3 \\  
  \-partition\_offset 16 \\  
  \-b boot/grub/i386-pc/eltorito.img \\  
     \-no-emul-boot \-boot-load-size 4 \-boot-info-table \--grub2-boot-info \\  
  \-eltorito-alt-boot \\  
  \-e boot/grub/efi.img \\  
     \-no-emul-boot \-isohybrid-gpt-basdat \\  
  \-o "$HOME/$OUTPUT\_ISO" \\  
  .

echo "--- ✅ BUILD COMPLETE: $HOME/$OUTPUT\_ISO \---"

**🚀 Execution Steps**

**1\. Generate Assets:**

bash v1\_1\_assets.sh

	**2\. Build ISO (Nuclear Option):**

sudo bash build\_nuclear\_v11.sh

	**3\. Exfiltrate:**

sudo mv /root/Lindows\_AU\_V1.1.iso \~/Lindows\_AU\_V1.1.iso  
sudo chown $USER:$USER \~/Lindows\_AU\_V1.1.iso  
gcloud storage cp \~/Lindows\_AU\_V1.1.iso gs://demo.spers.online/
