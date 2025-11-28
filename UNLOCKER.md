### **5\. The "End of Life" Unlocker**

**Filename:** `scripts/donation_mode.sh` *Usage: Run manually by admin to unlock full Linux power.*

\#\!/bin/bash  
\# LINDOWS AU \- DONATION MODE  
\# Unlocks the workstation for the next owner.

if \[ "$EUID" \-ne 0 \]; then   
    zenity \--error \--text="Please run as Administrator (sudo)."  
    exit  
fi

zenity \--question \\  
       \--title="Unlock Workstation Mode?" \\  
       \--text="This will unhide all system tools (Terminal, Synaptic) and restore the full App Store.\\n\\nUse this when donating the PC to a student or developer."

if \[ $? \-eq 0 \]; then  
   (  
   echo "20"; echo "\# Unhiding System Tools..."  
   grep \-l "NoDisplay=true" /usr/share/applications/\*.desktop | xargs sed \-i '/NoDisplay=true/d'  
     
   echo "60"; echo "\# Unlocking App Store..."  
   apt-get install \-y gnome-software-plugin-deb synaptic build-essential  
     
   echo "100"; echo "\# Done."  
   ) | zenity \--progress \--auto-close

   zenity \--info \--text="System Unlocked. Please Restart."  
fi