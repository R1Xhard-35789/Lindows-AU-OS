# **🏗️ The Build Journal: The Cloud Factory Protocol**

**"We didn't build this on a laptop. We built it on a 32-Core Supercomputer in Mexico."**

This document outlines the exact technical process used to compile **Lindows AU**. Because compiling an OS locally takes hours, we utilize high-performance cloud infrastructure to reduce build times to minutes.

## **⚡ The Strategy**

* **Infrastructure:** Google Cloud Compute Engine (GCE).  
* **Machine Type:** `e2-standard-32` (32 vCPU, 128GB RAM).  
* **Cost:** Approx \~$1.25 USD / Hour (Total build cost \< $5.00).  
* **Toolchain:** Ubuntu 24.04 LTS \-\> Cubic \-\> Custom Scripts \-\> ISO.

---

## **🛠️ Phase 1: Provisioning the Factory**

We spin up a "Disposable Build Server."

1. **Google Cloud Console** \-\> Compute Engine \-\> Create Instance.  
2. **OS:** Ubuntu 24.04 LTS Minimal (x86/64).  
3. **Disk:** 100GB Balanced Persistent Disk.  
4. **Network:** Allow HTTP/HTTPS Traffic.  
5. **Machine:** `e2-standard-32` (Or whatever high-CPU instance is cheap in your region).

**Architect's Note:** We use the "Spot" or standard pricing, but **STOP** the instance immediately after exfiltrating the ISO to minimize costs.

---

## **🖥️ Phase 2: The Visual Interface**

Since `Cubic` (Custom Ubuntu ISO Creator) requires a GUI, but cloud servers are headless (CLI only), we install a lightweight desktop and tunnel it via **Chrome Remote Desktop**.

### **The Setup Script (Run via SSH)**

Bash

\# Update & Install XFCE (Lightweight Desktop)  
sudo apt update && sudo apt install \-y xfce4 xfce4-goodies

\# Install Chrome Remote Desktop  
wget https://dl.google.com/linux/direct/chrome-remote-desktop\_current\_amd64.deb  
sudo apt install \-y ./chrome-remote-desktop\_current\_amd64.deb

\# Force XFCE Session  
sudo bash \-c 'echo "exec /etc/X11/Xsession /usr/bin/xfce4-session" \> /etc/chrome-remote-desktop-session'  
sudo systemctl disable lightdm.service

\# Install Build Tools (Cubic, Git, etc.)  
sudo apt-add-repository ppa:cubic-wizard/release \-y  
sudo apt update  
sudo apt install \-y cubic git python3-pip python3-tk zenity flatpak gtk2-engines-murrine qt5-style-plugins

## **💉 Phase 3: The Injection (The "Soul" of Lindows)**

We do not manually configure the OS. We inject a **Master Script** that rips out the default Ubuntu interface and installs the Lindows environment.

### **1\. The Staging Area**

We create a local folder `~/Lindows_Assets` containing our Python and Bash scripts (available in the `/scripts` folder of this repo):

* `lindows_welcome.py`: The First Run Wizard.  
* `lockdown_store.sh`: The "Walled Garden" security script.  
* `build_desktop.sh`: The layout generator.

### **2\. The Cubic Process**

1. **Source:** `ubuntu-24.04.3-desktop-amd64.iso`  
2. **Extraction:** Cubic extracts the ISO filesystem.  
3. **Transfer:** We push the assets from the Cloud VM into the Cubic Virtual Environment:

Bash  
\# Push assets into the build bubble  
sudo cp \-r \~/Lindows\_Assets/\* \~/Lindows\_Project/custom-root/opt/assets/

4\. **Execution:** Inside the Cubic Terminal (`chroot`), we fire the laser:

Bash  
bash /opt/assets/install\_core.sh

## **📦 Phase 4: Exfiltration**

Once the ISO is generated (using `zstd` compression for speed), we extract the 6GB file from the cloud server.

**Method A: The Bucket Dump (Enterprise)**

Bash  
gcloud auth login \--no-launch-browser  
gcloud storage cp \~/Lindows\_Project/\*.iso gs://your-public-bucket/

Method B: The HTTP Tunnel (Hacker Style)

Bash  
cd \~/Lindows\_Project  
sudo python3 \-m http.server 80  
\# Download via http://\[EXTERNAL\_IP\_ADDRESS\] on local machine

## **⚠️ Notes for Builders**

* **The "Insecure Memory" Warning:** During the build, GPG may complain about "Insecure Memory." This is a side effect of the `chroot` environment and can be ignored.  
* **Dependencies:** The script requires `software-properties-common` and `curl` to be installed *before* attempting to add the Brave browser repo.  
* **The Kill Switch:** Always **STOP** the cloud instance after downloading the ISO to avoid a surprise bill.

