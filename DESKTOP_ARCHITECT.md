### **4\. The Desktop Architect**

**Filename:** `scripts/build_desktop.sh` *Usage: Called by install\_core.sh.*

\#\!/bin/bash  
\# LINDOWS AU \- DESKTOP LAYOUT  
\# Creates the "Appliance" feel in /etc/skel

SKEL="/etc/skel/Desktop"  
GAMES="$SKEL/Games"  
APPS="$SKEL/Essential Apps"

mkdir \-p "$SKEL" "$GAMES" "$APPS"

\# \--- 1\. CREATE SHORTCUTS \---

\# File Explorer  
cat \<\<EOF \> "$SKEL/File Explorer.desktop"  
\[Desktop Entry\]  
Version=1.0  
Type=Application  
Name=File Explorer  
Exec=thunar  
Icon=system-file-manager  
Terminal=false  
EOF

\# Notepad  
cat \<\<EOF \> "$SKEL/Notepad.desktop"  
\[Desktop Entry\]  
Version=1.0  
Type=Application  
Name=Notepad  
Exec=mousepad  
Icon=text-editor  
Terminal=false  
EOF

\# \--- 2\. COPY & RENAME SYSTEM APPS \---

\# Word Processor  
cp /usr/share/applications/libreoffice-writer.desktop "$SKEL/Word Processor.desktop" 2\>/dev/null  
sed \-i 's/^Name=.\*/Name=Word Processor/' "$SKEL/Word Processor.desktop" 2\>/dev/null

\# Spreadsheet  
cp /usr/share/applications/libreoffice-calc.desktop "$SKEL/Spreadsheet.desktop" 2\>/dev/null  
sed \-i 's/^Name=.\*/Name=Spreadsheet/' "$SKEL/Spreadsheet.desktop" 2\>/dev/null

\# Brave  
cp /usr/share/applications/brave-browser.desktop "$SKEL/Brave Internet.desktop" 2\>/dev/null  
sed \-i 's/^Name=.\*/Name=Brave Internet/' "$SKEL/Brave Internet.desktop" 2\>/dev/null

\# \--- 3\. PERMISSIONS \---  
chmod \+x "$SKEL/"\*.desktop