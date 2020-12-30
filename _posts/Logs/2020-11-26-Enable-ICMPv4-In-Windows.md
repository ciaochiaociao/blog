---
title: Enable ICMPv4 (Ping) in Windows
---

```powershell
Get-NetFirewallRule -DisplayName ICMPv4
New-NetFirewallRule -DisplayName ICMPv4 -Direction Inbound -Action Allow -Protocol icmpv4 -Enabled True
```



