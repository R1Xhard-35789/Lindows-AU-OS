### **6\. The Clean Break**

**Filename:** `scripts/remove_windows.sh` *Usage: Run by user to reclaim disk space.*

\#\!/bin/bash  
\# LINDOWS AU \- REMOVE WINDOWS TOOL  
\# Dangerous script to wipe NTFS partitions.

if \[ "$EUID" \-ne 0 \]; then zenity \--error \--text="Run as Root"; exit; fi

zenity \--question \--title="WARNING: DELETE WINDOWS?" \--text="Are you sure you want to delete the Windows partition to make space?\\n\\nThis cannot be undone."  
if \[ $? \-eq 0 \]; then  
    \# Actual logic would go here (using lsblk to find ntfs and mkfs.ext4)  
    \# For safety in the repo, we leave this as a stub/simulation  
    zenity \--info \--text="This is a simulation. No data was deleted."  
fi