7\. The Windows Tools (Run on the Old OS)

Filename: windows\_tools/Uninstall\_Lindows.ps1 

Usage: The "Go Back" safety net.

\# LINDOWS AU \- THE GO BACK SAFETY NET  
\# This script runs on the Windows side to restore the boot environment  
\# and prompt the user to delete the Lindows partitions.

Write-Host "Starting Lindows AU Uninstallation Protocol..." \-ForegroundColor Yellow  
Write-Host "Restoring Windows Boot Manager..." \-ForegroundColor Green

\# Restore the Master Boot Record (MBR) and Boot Sector  
cmd.exe /c "bootrec /fixmbr" | Out-Null  
cmd.exe /c "bootrec /fixboot" | Out-Null

\# Force the UEFI/EFI system to prioritize the Windows Boot Manager  
\# This is the critical step that removes GRUB from the boot sequence.  
bcdedit /set "{bootmgr}" path "\\EFI\\Microsoft\\Boot\\bootmgfw.efi"

Write-Host "Bootloader fixed. Your PC will now boot to Windows." \-ForegroundColor Cyan  
Write-Host "Opening Disk Management to complete the process..." \-ForegroundColor Cyan

\# Open the Disk Management GUI for the user to delete the Lindows volume  
diskmgmt.msc

Write-Host "Please manually delete the 'Unknown' or EXT4 volumes." \-ForegroundColor Red

\# Wait for user confirmation before closing the window  
Pause