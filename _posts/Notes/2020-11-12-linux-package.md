---
title: Linux Package System
---

## Install from binary (with `apt`, and `add-apt-repository` from `software-properties-common`)
```output
apt list | grep python3.  # xenial (16.04) comes with python 3.5 at most by default

# The software-properties-common package gives you better control over your package manager by letting you add PPA (Personal Package Archive) repositories.
sudo apt install software-properties-common

apt show software-properties-common
dpkg -l software-properties-common
dpkg -L software-properties-common | grep '/bin'  # -L|--listfiles <package> ...     List files 'owned' by package(s).

# Deadsnakes is a PPA with newer releases of Python than the default Ubuntu repositories.
sudo add-apt-repository ppa:deadsnakes/ppa

sudo apt update
sudo apt install python3.X
apt install python3-pip  # for python<=3.7
```

## Install from source

```
# For Compiling a package from source code requires additional software.
sudo apt install build-essential zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev libssl-dev libreadline-dev libffi-dev wget

cd /tmp; wget http://<url>.<to>.<package>.tgz/tar.gz
tar –xf <package>.tgz
cd <package>
./configure ––enable–optimizations
sudo make install
```

```

# --- Intall Python from source code as an example ---
# do the above things
...

# second installation: If you used the altinstall method, you have two different versions of Python on your system at the same time. Each installation uses a different command. e.g. python2 python3 python3.X
sudo make altinstall

# overwrite default
sudo make install
```

## Change the default Python Version by `update-alternatives`

```
# used commands
sudo update-alternatives --install <link> <name> <path> <priority>  # add a group of alternatives to the system.
<link> is the symlink pointing to /etc/alternatives/<name>.
  (e.g. /usr/bin/pager)
<name> is the master name for this link group.
  (e.g. pager)
<path> is the location of one of the alternative target files.
  (e.g. /usr/bin/less)
<priority> is an integer; options with higher numbers have higher priority in
  automatic mode.
  
sudo update-alternatives --set <name> <path>  # set <path> as alternative for <name>.

sudo update-alternatives --config  # show alternatives for the <name> group and ask the user to select which one to use.

# ---
sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.9 2
sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.5 1
```

## Install pip for python>=3.8 on Xenial (16.04 Ubuntu) (not working)

Known issue:

- `python3-pip` is a package in default ubuntu packages, of which the version depends on the default python version coming with its Ubuntu distribution. For example, in Xenial (16.04), it comes with python 3.5. So it does not guarantee to work in other versions.

- if you install `python3-pip` by `apt install` , all version of python use the same script. For python <= 3.7, it generally works. But for python >= 3.8, it failed with an error message:

  ```
  Traceback (most recent call last):
    File "/usr/lib/python3.9/runpy.py", line 197, in _run_module_as_main
      return _run_code(code, main_globals, None,
    File "/usr/lib/python3.9/runpy.py", line 87, in _run_code
      exec(code, run_globals)
    File "/usr/lib/python3/dist-packages/pip/__main__.py", line 19, in <module>
      sys.exit(pip.main())
    File "/usr/lib/python3/dist-packages/pip/__init__.py", line 217, in main
      return command.main(cmd_args)
    File "/usr/lib/python3/dist-packages/pip/basecommand.py", line 242, in main
      with self._build_session(
    File "/usr/lib/python3/dist-packages/pip/basecommand.py", line 66, in _build_session
      session = PipSession(
    File "/usr/lib/python3/dist-packages/pip/download.py", line 321, in __init__
      self.headers["User-Agent"] = user_agent()
    File "/usr/lib/python3/dist-packages/pip/download.py", line 93, in user_agent
      zip(["name", "version", "id"], platform.linux_distribution()),
  AttributeError: module 'platform' has no attribute 'linux_distribution'
  ```

  >  `platform.linux_distribution` was removed in Python 3.8. See: [bugs.python.org/issue28167](https://bugs.python.org/issue28167), which seems to affect the package setup/install scripts. See this [comment](https://stackoverflow.com/questions/58758447/how-to-fix-module-platform-has-no-attribute-linux-distribution-when-instal#comment103804825_58758447)

- Use `sudo python3.X -m easy_install pip` instead. But note that `pip install -f <url>` can not find specific version of `torch` due to unknown reason.

## Install pip for Python <=3.7 on Xenial (16.04 Ubuntu)

```
sudo apt install python3-pip
pip3 -V
python3.7 -m pip list
hash -d pip3  # or
hash -r  # forget all
```



## Update  remembered locations of commands using `hash`

```
hash
# used to list a hash table of recently executed commands. It is used for views, resets, or manually changes within the bash path hash. It keeps the locations of recently executed programs and shows them whenever we want to see it. It provides a complete pathname of each command name.
# 
-r	Forget all the remembered locations.
-d	Forget the remembered locations of command_name.
-t	Print the remembered location of each command_name. If multiple command_names are given there, precede each location with corresponding command_name
```



```
$ which pip3
/usr/bin/pip3
$ pip3  
/usr/local/bin/pip3: No such file or directory
$ type pip3
pip3 is hashed (/usr/local/bin/pip3)
$ hash -t pip3
/usr/local/bin/pip3
$ hash -r
$ type pip3
pip3 is hashed (/usr/bin/pip3)
```

