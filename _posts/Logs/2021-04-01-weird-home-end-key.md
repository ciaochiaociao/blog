---
title: Weird Home End Keys And tmux mouse mode not working when ssh from windows to linux
---

**Description:**

When `ssh` in a zsh shell (bash is fine)

1. `Home` Key turns into an **escape sequence** (starting with `ESC`)
2. `End` Key turns into `~`
3. `tmux` mouse mode does not work 

**Reproduce:**

1. Open a Windows **Command Prompt**/**PowerShell**
2. ssh to a zsh shell in a remote Linux (`WSL` was not tested)
3. type `Home`, `End`

**Cause:**

```
# in Command Prompt
$ ssh -V
OpenSSH_for_Windows_7.7p1, LibreSSL 2.6.5
```

**The Windows build of OpenSSH 7.7** has its own **“input generator”** that does not properly handle Home and End.

### **Solution**:

Upgrade to **OpenSSH 8.1**

- upgrade
  ```
  choco install openssh --version 8.1.0-beta
  ```

- Then, make sure you have `C:\Program Files\OpenSSH-Win64` before `C:\Windows\System32\OpenSSH\` in your `PATH` environment variable:

**Workaround:**

**WSL** uses a different that does not have this issue:

- Use **WSL**(e.g., *Windows Terminal*) to open a linux terminal (bash) on windows instead of the *Command Prompt*
- `ssh` to the remote

**related issue:**

2020/11/06

https://github.com/microsoft/terminal/issues/8179#issuecomment-723181872

> The Windows build of OpenSSH 7.7 has its own “input generator” that does not properly handle Home and End. You can upgrade to a newer version of use the SSH client from WSL. 😄

https://github.com/microsoft/terminal/issues/8179#issuecomment-724698524

> The issue still happens with OpenSSH 8.0 but it's fixed with OpenSSH 8.1. Thank you.

```

```