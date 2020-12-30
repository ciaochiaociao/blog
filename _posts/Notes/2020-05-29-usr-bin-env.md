---
title: usr bin env
---

Using `#!/usr/bin/env python` instead of the absolute (full path) `#!/usr/bin/python` makes sure python (or any other language's interpreter) is found, in case it might not be in exactly the same location across different Linux- or Unix -like distributions, as explained e.g. [here](http://www.cyberciti.biz/tips/finding-bash-perl-python-portably-using-env.html).

### About env


`env` is an executable in `/usr/bin`, available as a compatibility symlink in `/usr/bin` to `env`

env is a shell command for Unix and Unix-like operating systems. It is used to either print a list of environment variables or run another utility in an altered environment without having to modify the currently existing environment. Using env, variables may be added or removed, and existing variables may be changed by assigning new values to them.

an executable is executed by `execve`. `execve` demands a full (valid) path to the interpreter.

https://www.cyberciti.biz/tips/finding-bash-perl-python-portably-using-env.html

https://askubuntu.com/questions/716280/why-do-some-python-scripts-begin-with-usr-bin-env-python