---

title: Setup Ubuntu
typora-root-url: ../..
---

## 

## Partition Plan

| path              | name     | main Filesystem | disk               | note              |
| ----------------- | -------- | --------------- | ------------------ | ----------------- |
| /dev/sda (Disk 0) | ?        | Hybrid?         | Samsung 500 GB     | Ubuntu            |
| /dev/sdb (Disk 2) | OS (C)   | NTFS            | Kingston 128GB SSD | Microsoft Windows |
| /dev/sdc (Disk 1) | data (D) | NTFS            | 1TB HDD            | data              |

 - /dev/sda2

| path      | name       | partition   | Partition Type       | Size            | Filesystem | Contents                               |
| --------- | ---------- | ----------- | -------------------- | --------------- | ---------- | -------------------------------------- |
| /dev/sdb1 | -          | Partition 1 | Microsoft Reserved   | ?               | ?          | Unknown                                |
| /dev/sdb2 | SYSTEM     | Partition 2 | EFI System Partition | 273 (260) MB    | ?          | EFI System                             |
| /dev/sdb3 | OS         | Partition 3 | Basic Data Partition | 127 (118.28) GB | NTFS       | Basic Data                             |
| /dev/sdb4 | Filesystem | Partition 4 | Recovery Partition   | 611 (583) MB    | ?          | Microsoft Windows Recovery Environment |
| /dev/sda2 |            |             | Primary Partition    | (476.94) GB     | ?          |                                        |
| /dev/sdc? |            |             | Basic Data Partition | (931.39) GB     | NTFS       |                                        |

## Install Ubuntu

- Typora
- Chrome
- Skype
- Spotify
- LINE
- powerline font
- VSCode/PyCharm
- Winapps
- Superpaper

## Winapps

- Use kvm/qemu as the backend by default. But one can also use VirtualBox

## Superpaper

ref: https://github.com/hhannine/superpaper/blob/master/docs/installation-linux.md

### AppImage file (portable)

download from https://github.com/hhannine/superpaper/releases and make the file executable, then it is able to run.

### Install from PyPI (embedded)

```
sudo apt install python3-wxgtk4.0  # (like GUI for Python, similar to gtk)
python3 -m pip install --user --upgrade superpaper
```

## Replace Mutter (GNOME) with Compiz (X Window System) for the Window Manager (abandoned) 

tldr: Not available since GNOME does not use Compiz but Mutter/Wayland. Use Wayfire instead.

ref: https://help.ubuntu.com/community/CompositeManager#Compiz

- Compiz:
  - use QT/GTK? toolkit, X Window System
- Mutter:
  - use GTK+, Wayland (X Window System)

```bash
$ sudo apt-get install compiz compizconfig-settings-manager compiz-plugins-extra compiz-plugins-main compiz-plugins
$ compiz --replace
System crashed !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

# solution: (not tested yet) from https://www.youtube.com/watch?v=1cjaMJjKJ_U
$ sudo apt install lxqt-panel  # or lxpanel
$ lxqt-panel
$ compiz --replace
```

## Replace Mutter (GNOME) with Wayfire (Not done yet)

ref: https://github.com/WayfireWM/wayfire

tldr: setup dependencies too complicated

1. Enable Wayland 

```bash
systemctl status display-manager.service
cat /usr/ports/graphics/wayland
sudoedit /etc/gdm3/custom.conf

# --- in file ---
# comment the line below to force the login screen to use Xorg
#WaylandEnable=false
```

2. Re-login
3. 

## Wine

```
sudo dpkg --add-architecture i386  # If your system is 64 bit, enable 32 bit architecture (if you haven't already)

wget -nc https://dl.winehq.org/wine-builds/winehq.key
sudo apt-key add winehq.key

sudo add-apt-repository 'deb https://dl.winehq.org/wine-builds/ubuntu/ focal main'  # for Ubuntu 20.04

sudo apt update
sudo apt install --install-recommends winehq-stable

wine ***.exe

# install WINE Mono for the first time running which is required to run .NET app
```

## WinApps

1. Create a VM using any Virtual Machine Manager, e.g., VirtualBox, VMWare, kvm (with name RDPWindows) and set the Network to **Bridged Adapter** (in VirtualBox as an example), the **Remote Display** enabled

![virtualbox-network-settings-for-winapps](/assets/images/virtualbox-network-settings-for-winapps.png)

![image-20201225172933849](/assets/images/image-20201225172933849.png)

2. Start the VM and follow https://github.com/Fmstrat/winapps/blob/main/docs/KVM.md#install-the-virtual-machine
   1. Install Win10 OS and Office (e.g. from the software package server from Academia Sinica)
      - remember to either mount the `.iso` installation file before starting or share folder where they reside.
   2. Install **Windows VirtIO Drivers** Inside the VM
      - https://pve.proxmox.com/wiki/Windows_VirtIO_Drivers
   - https://github.com/virtio-win
      ![File:Win10-virtio-driver-wizard.png](/assets/images/780px-Win10-virtio-driver-wizard.png)
   3. Download https://github.com/Fmstrat/winapps/blob/main/install/RDPApps.reg and merge Registry file in the guest OS
      ![img](/assets/images/25.png)
   4. Rename the guest OS
       ![img](/assets/images/27.png)
5. Turn on Remote Desktop function in the guest OS
     ![img](/assets/images/30.png)
   
   6. Turn on ICMP feature (for debugging)

```powershell
Get-NetFirewallRule -DisplayName ICMPv4
New-NetFirewallRule -DisplayName ICMPv4 -Direction Inbound -Action Allow -Protocol icmpv4 -Enabled True
```

3. Configure and install `WinApps` on the host OS (Ubuntu) (require `freerdp2-x11`)
```bash
# get ip of guest OS (windows) from host OS
$ VBoxManage guestproperty enumerate RDPWindows | grep IP
<ip_address>
$ ping <ip_address>

$ sudo apt-get install -y freerdp2-x11  # prerequisite
$ git clone https://github.com/Fmstrat/winapps.git  # download
$ cd winapps

$ vi ~/.config/winapps/winapps.conf  # configure (put the content below)
RDP_USER="MyWindowsUser"
RDP_PASS="MyWindowsPassword"
#RDP_DOMAIN="MYDOMAIN"
RDP_IP="<ip_address>"
#RDP_SCALE=100
#RDP_FLAGS=""
#MULTIMON="true"
#DEBUG="true"

$ bin/winapps check  # check before installation
$ ./installer.sh  # install
```



## VirtualBox In Ubuntu

ref: https://stackoverflow.com/questions/61248315/sign-virtual-box-modules-vboxdrv-vboxnetflt-vboxnetadp-vboxpci-centos-8

ref: https://stegard.net/2016/10/virtualbox-secure-boot-ubuntu-fail/

- Note that one has to sign MOK key for using VirtualBox in Ubuntu if disabling secure boot is not wanted.

```
# Solution 1 : disable secure boot.

# Solution 2 : 

$ sudo -i
$ mkdir /root/module-signing
$ cd /root/module-signing

# 1. Create MOK key by `openssl`
$ openssl req -new -x509 -newkey rsa:2048 -keyout MOK.priv -outform DER -out MOK.der -nodes -days 36500 -subj "/CN=YOUR_NAME/"  # e.g. /CN=VirtualBox/
[...]
$ chmod 600 MOK.priv

# 2. Import MOK key (RSA) by `mokutil`
$ apt install mokutil  # if not found
$ mokutil --import /root/module-signing/MOK.der
input password:
input password again:

# 3. Enroll Machine Owner Key (MOK you created) by "MOK manager EFI utility"
# Reboot the machine. When the bootloader starts, the MOK manager EFI utility should automatically start. It will ask for parts of the password supplied in step 3. Choose to “Enroll MOK”, then you should see the key imported

# check with 
$ dmesg | grep 'EFI' | grep cert


# 4. Sign kernel modules by `sign-file` script in linux-headers kernel folder

$ cd /root/module-signing
$ vi sign-virtual-box

#!/bin/bash

for modfile in $(dirname $(modinfo -n vboxdrv))/*.ko; do
  echo "Signing $modfile"
  /usr/src/linux-headers-$(uname -r)/scripts/sign-file sha256 \
                                /root/module-signing/MOK.priv \
                                /root/module-signing/MOK.der "$modfile"
done

$ chmod 700 sign-virtual-box
$ ./sign-virtual-box 

# 5. Add kernel module vboxdrv by `modprobe`
$ sudo modprobe vboxdrv
$ sudo modprobe vboxnetflt  # this will also fix numerous errors in Network settings

# 6. Install the extension pack when needed
# 7. Install the Addition Packs in Guest OS when you need to, e.g., access the shared folder
```

