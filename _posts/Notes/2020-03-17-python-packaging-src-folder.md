---
title: Python Packaging (src folder)
---

ref: https://blog.ionelmc.ro/2014/05/25/python-packaging/

ref: https://medium.com/pyladies-taiwan/python-%E7%9A%84-import-%E9%99%B7%E9%98%B1-3538e74f57e3

ref: https://www.python.org/dev/peps/pep-0328/#guido-s-decision

ref: https://realpython.com/absolute-vs-relative-python-imports/

**good ref**: https://stackoverflow.com/questions/14132789/relative-imports-for-the-billionth-time

`pip install -e`: https://www.reddit.com/r/learnpython/comments/ayx7za/how_does_pip_install_e_work_is_there_a_specific/

## Workflow
1. sys.modules (a cache of all modules that have been previously imported.)
2. Python Standard Library
3. sys.path
	- PYTHONPATH
	- This list usually includes the current directory, which is searched first.

## Import
-  You can import both packages and modules. (Note that importing a package essentially imports the package’s __init__.py file as a module.)

## Absolute Import
An absolute import specifies the resource to be imported using its full path from the project’s root folder.