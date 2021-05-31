---
title: Python Import
---

ref: https://blog.ionelmc.ro/2014/05/25/python-packaging/

ref: https://medium.com/pyladies-taiwan/python-%E7%9A%84-import-%E9%99%B7%E9%98%B1-3538e74f57e3

ref: https://www.python.org/dev/peps/pep-0328/#guido-s-decision

ref: https://realpython.com/absolute-vs-relative-python-imports/

**good ref**: https://stackoverflow.com/questions/14132789/relative-imports-for-the-billionth-time

## Workflow

tldr:

1. sys.modules (a cache of all modules that have been previously imported.)
2. Python Standard Library
3. sys.path
   - PYTHONPATH
   - This list usually includes the current directory, which is searched first.

In details:

 1. First, they look for your module or package in `sys.modules`, where Python stores your previously imported code.
 2.  If Python cannot find the module there, it will then search through the Python Standard Library for it.
 3.  If Python still cannot find the module, it will go through your entire storage space, starting with the current directory and the ones listed in your `system.path`
 4.  If the module is found in these places, it will add the module to your program
 5.  otherwise, it will give a `ModuleNotFoundError`

> imported -> stdlib -> cwd, sys.path

## import

You can import both packages and modules. (Note that importing a package essentially imports the package’s __init__.py file as a module.)

```
from mypackage import mymodule
```

mymodule: 
- code
- module
- subpackage


## Absolute Imports
 - An absolute import specifies the resource to be imported using its full path from the project’s root folder.
 - include the entire path to your script, starting with the program's root folder


## start your program from a subfolder, or in any other situation where your sys.path may change.

 - start your subfolder script as an imported module
 - append sys.path directly in your code.

## File path in submodule/module imported from subfolder
```
wd = os.path.dirname(__file__)
fpath = os.path.join(wd, fpath)
```


