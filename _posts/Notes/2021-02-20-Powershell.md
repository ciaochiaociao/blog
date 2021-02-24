---
title: Powershell
---

## Cookbook

- Copy all files in any child directories that have a name containing a string to a folder

```
Get-ChildItem -Path \\140.109.19.65\iisNLU.GroupMeeting -Recurse -Directory |
>> Where-Object -Property Name -Match '^.*Chiao-Wei.*$' | Get-ChildItem | Copy-Item -Destination reports
```

