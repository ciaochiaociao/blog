---
title: Python Import
---

## Steps
 1. First, they look for your module or package in `sys.modules`, where Python stores your previously imported code.
 2.  If Python cannot find the module there, it will then search through the Python Standard Library for it.
 3.  If Python still cannot find the module, it will go through your entire storage space, starting with the current directory and the ones listed in your `system.path`
 4.  If the module is found in these places, it will add the module to your program
 5.  otherwise, it will give a `ModuleNotFoundError`

> imported -> stdlib -> cwd, sys.path


## `import`
```
from mypackage import mymodule
```

mymodule: 
- code
- module
- subpackage


## Absolute Imports
 - include the entire path to your script, starting with the program's root folder


## start your program from a subfolder, or in any other situation where your sys.path may change.

 - start your subfolder script as an imported module
 - append sys.path directly in your code.

## File path in submodule/module imported from subfolder
```
wd = os.path.dirname(__file__)
fpath = os.path.join(wd, fpath)
```