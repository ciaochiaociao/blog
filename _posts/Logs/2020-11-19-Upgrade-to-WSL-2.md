---
title: Upgrade from WSL 1 to WSL 2
---
ref: https://docs.microsoft.com/en-us/windows/wsl/install-win10

ref: https://docs.microsoft.com/en-us/windows/wsl/wsl2-kernel

ref: https://docs.microsoft.com/en-us/windows/wsl/compare-versions#exceptions-for-using-wsl-1-rather-than-wsl-2

- port forwarding from host to virtual network adapter of WSL 2
- resize the virtual machine of WSL2 if the default 256GB has been used up

```powershell
# Step 1 - enable WSL 1 if not existing
$ # dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart

# Step 2 - check if it can Update to WSL 2
# For x64 systems: Version 1903 or higher, with Build 18362 or higher.
$ ver  # in cmd
$ winver  # in powershell 
Version 2004
OS build 19041.630

# Step 3 - Enable Virtual Machine feature
$ dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
Deployment Image Servicing and Management tool
Version: 10.0.19041.572

Image Version: 10.0.19041.630

Enabling feature(s)
[==========================100.0%==========================]
The operation completed successfully.

# Restart your machine to complete the WSL install and update to WSL 2.

# Step 4 - Download the Linux kernel update package from https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi

# Step 5 - Set WSL 2 as your default version
$ wsl --set-default-version 2
Conversion in progress, this may take a few minutes...
For information on key differences with WSL 2 please visit https://aka.ms/wsl2

# Step 6 - Install Distro
# download from Windows Store

# Step 7 - "Register" a Distro in WSL
# run <distro> and it will configure once and for all

# Optional Step - Set existing distro to version 2
$ wsl --set-version Ubuntu 2
The requested operation could not be completed due to a virtual disk system limitation.  Virtual hard disk files must be uncompressed and unencrypted and must not be sparse.
# solution - deselect the compression option in Property for the folder C:\Users\<user>\AppData\Local\Packages\CanonicalGroupLimited.UbuntuonWindows_79rhkp1fndgsc\LocalState (ref: https://github.com/microsoft/WSL/issues/4103)

```

```powershell
# for insider
wsl -u <user> -d/--distribution <distro>
wsl --unregister <distro>

wslconfig /list /all /running /setdefault /terminate /unregister
```


```powershell
# Step 1 - enable WSL 1 if not existing
$ # dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart

# Step 2 - check if it can Update to WSL 2
# For x64 systems: Version 1903 or higher, with Build 18362 or higher.
$ ver  # in cmd
$ winver  # in powershell 
Version 2004
OS build 19041.630

# Step 3 - Enable Virtual Machine feature
$ dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
Deployment Image Servicing and Management tool
Version: 10.0.19041.572

Image Version: 10.0.19041.630

Enabling feature(s)
[==========================100.0%==========================]
The operation completed successfully.

# Restart your machine to complete the WSL install and update to WSL 2.

# Step 4 - Download the Linux kernel update package from https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi

# Step 5 - Set WSL 2 as your default version
$ wsl --set-default-version 2
Conversion in progress, this may take a few minutes...
For information on key differences with WSL 2 please visit https://aka.ms/wsl2

# Step 6 - Install Distro
# download from Windows Store

# Step 7 - "Register" a Distro in WSL
# run <distro> and it will configure once and for all

# Optional Step - Set existing distro to version 2
$ wsl --set-version Ubuntu 2
The requested operation could not be completed due to a virtual disk system limitation.  Virtual hard disk files must be uncompressed and unencrypted and must not be sparse.
# solution - deselect the compression option in Property for the folder C:\Users\<user>\AppData\Local\Packages\CanonicalGroupLimited.UbuntuonWindows_79rhkp1fndgsc\LocalState (ref: https://github.com/microsoft/WSL/issues/4103)

```

port forwarding from host to virtual network adapter of WSL 2

```powershell
ip addr | grep eth0  # in host's powershell
# or
ifconfig  # in WSL
# inet: 172.24.206.209 ...

netsh interface portproxy add v4tov4 listenport=22 listenaddress=0.0.0.0 connectport=22 connectaddress=<address>

netsh interface portproxy show v4tov4
```

####  

## Others

- per distro: `/etc/wsl.conf`
- globally: `C:\Users\<username>\.wslconfig`