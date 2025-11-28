### **2\. The First Run Wizard**

**Filename:** `scripts/lindows_welcome.py` *Usage: Runs automatically on the first login.*

\#\!/usr/bin/env python3  
import subprocess  
import os  
import sys

\# LINDOWS AU \- WELCOME WIZARD  
\# Handles Browser Choice and Suitcase Import

def zenity(args):  
    """Wrapper for Zenity dialogs"""  
    try:  
        cmd \= \['zenity'\] \+ args  
        res \= subprocess.run(cmd, stdout=subprocess.PIPE, stderr=subprocess.PIPE, text=True)  
        return res.returncode \== 0, res.stdout.strip()  
    except Exception as e:  
        return False, str(e)

def main():  
    \# 1\. Welcome Message  
    zenity(\['--info',   
            '--title=Welcome to Lindows',   
            '--text=\<b\>Welcome to your new computer\!\</b\>\\n\\nSafe. Simple. Private.\\n\\nLet\\'s get you set up.',   
            '--width=400',   
            '--icon-name=computer'\])  
      
    \# 2\. Browser Ballot  
    success, choice \= zenity(\['--list',   
                              '--title=Choose Your Internet',   
                              '--text=Which browser do you prefer?',   
                              '--column=Browser', '--column=Description',  
                              'Brave', 'Blocks Ads & Trackers (Recommended)',  
                              'Firefox', 'The Open Source Standard',  
                              '--height=300', '--width=500'\])  
      
    if success and choice:  
        if choice.startswith('Brave'):  
            os.system("xdg-settings set default-web-browser brave-browser.desktop")  
        else:  
            os.system("xdg-settings set default-web-browser firefox.desktop")

    \# 3\. Suitcase Check  
    success, \_ \= zenity(\['--question',   
                         '--title=Data Transfer',   
                         '--text=\<b\>Did you pack a Digital Suitcase?\</b\>\\n\\nIf you have a USB drive with your passwords from Windows, click YES.',   
                         '--width=500'\])  
      
    if success:  
        zenity(\['--info', '--text=Please insert your USB drive now.\\n\\nWhen ready, open the "File Explorer" and look for your Suitcase file.'\])

    \# 4\. Self-Destruct (Remove from Autostart)  
    try:  
        autostart \= os.path.expanduser("\~/.config/autostart/lindows-welcome.desktop")  
        if os.path.exists(autostart):  
            os.remove(autostart)  
    except:  
        pass

if \_\_name\_\_ \== "\_\_main\_\_":  
    main()