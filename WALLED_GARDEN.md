### **3\. The Walled Garden (Store Lockdown)**

**Filename:** `scripts/lockdown_store.sh` *Usage: Called by install\_core.sh.*

\#\!/bin/bash  
\# LINDOWS AU \- STORE SECURITY  
\# Removes Snap, Hides Debs, Enforces Flatpak

echo "Locking down App Store..."

\# 1\. Purge Snap  
apt-get purge \-y snapd snap-store  
apt-mark hold snapd

\# 2\. Configure Gnome Software  
apt-get install \-y gnome-software gnome-software-plugin-flatpak flatpak

\# 3\. Remove Dangerous Plugins (Hide System Packages)  
apt-get remove \-y gnome-software-plugin-deb packagekit

\# 4\. Add Flathub  
flatpak remote-add \--if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo

\# 5\. Redirect Shortcuts  
\# Make the "Software" icon open our safe store  
sed \-i 's/Exec=snap-store/Exec=gnome-software/' /usr/share/applications/org.gnome.Software.desktop 2\>/dev/null