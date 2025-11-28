**1\. The Master Injector**  
**Filename:** `scripts/install_core.sh` *Usage: This runs INSIDE Cubic to build the OS.*

\#\!/bin/bash  
\# LINDOWS AU \- CORE INSTALLER (v1.0)  
\# This script transforms a stock Ubuntu Minimal ISO into Lindows AU.

set \-e \# Exit on error

echo "--- STARTING LINDOWS CORE INJECTION \---"

\# 1\. FIX REPOSITORIES & INSTALL BASE TOOLS  
apt update  
apt install \-y software-properties-common git curl gpg wget nano  
add-apt-repository universe \-y  
add-apt-repository multiverse \-y  
apt update

\# 2\. INSTALL DESKTOP & CORE APPS  
\# Note: calamares-settings-ubuntu removed to prevent crash on minimal ISO  
apt install \-y xfce4 xfce4-goodies lightdm calamares \\  
    zenity python3-pip flatpak gnome-software-plugin-flatpak \\  
    libreoffice-writer libreoffice-calc libreoffice-impress libreoffice-gtk3 \\  
    vlc mpv thunar mousepad aisleriot gnome-mines \\  
    gtk2-engines-murrine qt5-style-plugins python3-tk

\# 3\. INSTALL BRAVE BROWSER  
curl \-fsSLo /usr/share/keyrings/brave-browser-archive-keyring.gpg https://brave-browser-apt-release.s3.brave.com/brave-browser-archive-keyring.gpg  
echo "deb \[signed-by=/usr/share/keyrings/brave-browser-archive-keyring.gpg\] https://brave-browser-apt-release.s3.brave.com/ stable main"| tee /etc/apt/sources.list.d/brave-browser-release.list  
apt update  
apt install \-y brave-browser

\# 4\. DEPLOY INTERNAL SCRIPTS  
\# Assumes assets were moved to /opt/assets inside Cubic before running this  
cp /opt/assets/lindows\_welcome.py /usr/local/bin/  
cp /opt/assets/donation\_mode.sh /usr/local/bin/  
cp /opt/assets/remove\_windows.sh /usr/local/bin/  
chmod \+x /usr/local/bin/\*.sh /usr/local/bin/\*.py

\# 5\. EXECUTE SUB-ROUTINES  
bash /opt/assets/lockdown\_store.sh  
bash /opt/assets/build\_desktop.sh

\# 6\. CONFIGURE AUTOSTART (Welcome Wizard)  
mkdir \-p /etc/skel/.config/autostart  
cat \<\<EOF \> /etc/skel/.config/autostart/lindows-welcome.desktop  
\[Desktop Entry\]  
Type=Application  
Name=Welcome Wizard  
Exec=/usr/local/bin/lindows\_welcome.py  
Hidden=false  
NoDisplay=false  
X-GNOME-Autostart-enabled=true  
EOF

\# 7\. INSTALL CHICAGO95 THEME  
echo "Installing Chicago95..."  
cd /tmp  
rm \-rf Chicago95  
git clone https://github.com/grassmunk/Chicago95.git  
mkdir \-p /usr/share/themes /usr/share/icons  
cp \-r Chicago95/Theme/Chicago95 /usr/share/themes/  
cp \-r Chicago95/Icons/\* /usr/share/icons/

\# 8\. CLEANUP  
apt autoremove \-y  
apt clean  
rm \-rf /tmp/\*

echo "----------------------------------------"  
echo "   LINDOWS CORE INJECTION COMPLETE"  
echo "----------------------------------------"