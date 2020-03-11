---
title: Pycharm Console Output Encoding
---

[Update]
https://intellij-support.jetbrains.com/hc/en-us/community/posts/360003543559-Console-output-has-wrong-encoding-in-log-file

It is a known limitation.

[Ref1]
https://www.jetbrains.com/help/pycharm/configuring-output-encoding.html

PyCharm creates files using the IDE encoding defined in the File Encodings page of the Settings / Preferences dialog Ctrl+Alt+S. You can use either the system default or select from the list of available encodings. By default, this encoding affects console output. If you want the encoding for console output to be different from the global IDE settings, configure the corresponding JVM option:

1. On the Help menu, click Edit Custom VM Options.

2. Add the -Dconsole.encoding option and set the value to the necessary encoding. For example: -Dconsole.encoding=UTF-8

3. Restart PyCharm.

(This above method does not work after trying)


## Cause
The PyCharm Console Output Encoding is determined by
 - the encoding of the terminal in which the python interpreter is

## Solution (Updated, Working)
Change the Python interpreter of Windows to Linux (e.g. WSL)