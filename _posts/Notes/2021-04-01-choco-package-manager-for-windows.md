---
title: Chocolatey (choco) Package Manager for Windows
---

**Requirements**:

- Windows 7+ / Windows Server 2003+

- PowerShell v2+ (minimum is v3 for install from this website due to TLS 1.2 requirement)

  ```powershell
  Get-Host | Select-Object Version
  ```

- .NET Framework 4+ (the installation will attempt to install .NET 4.0 if you do not have it installed)(minimum is 4.5 for install from this website due to TLS 1.2 requirement)

    ```powershell
    Get-ChildItem 'HKLM:\SOFTWARE\Microsoft\NET Framework Setup\NDP' -Recurse | Get-ItemProperty -Name version -EA 0 | Where { $_.PSChildName -Match '^(?!S)\p{L}'} | Select PSChildName, version
    ```

**Installation**

```powershell
> Get-ExecutionPolicy
Restricted
> Set-ExecutionPolicy AllSigned
# turn off real-time scanning and firewall in AntiVirus software (McAfee here) https://github.com/chocolatey/choco/issues/2132
> Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))

WARNING: Not setting tab completion: Profile file does not exist at
'C:\Users\cwhsu\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1'.

> choco /?
> Set-ExecutionPolicy Restricted
# turn on Firewall
```

