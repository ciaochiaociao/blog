---
title: Open Cmder Here
---

Enable (`ContextCmder-Enable.reg`)
```
Windows Registry Editor Version 5.00

[HKEY_CLASSES_ROOT\Directory\Background\shell\Cmder]
@="Open Cmder Here"
"Icon"="C:\\bin\\cmder\\Cmder.exe,0"

[HKEY_CLASSES_ROOT\Directory\Background\shell\Cmder\command]
@="\"C:\\bin\\cmder\\Cmder.exe\" \"%V\""

[HKEY_CLASSES_ROOT\Directory\shell\Cmder]
@="Open Cmder Here"
"Icon"="C:\\bin\\cmder\\Cmder.exe,0"

[HKEY_CLASSES_ROOT\Directory\shell\Cmder\command]
@="\"C:\\bin\\cmder\\Cmder.exe\" \"%1\""
```

![]({{site.baseurl}}/assets/images/OpenCmderHere.JPG)

Disable (`ContextCmder-Disable.reg`)
```
Windows Registry Editor Version 5.00

[-HKEY_CLASSES_ROOT\Directory\Background\shell\Cmder]
[-HKEY_CLASSES_ROOT\Directory\shell\Cmder]
```

ref: https://gist.github.com/MichalZalecki/a369f06f8ce6bc83b35d