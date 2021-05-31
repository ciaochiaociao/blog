---
title: Python Import/Modules/Packages (src folder)
---



`pip install -e`: https://www.reddit.com/r/learnpython/comments/ayx7za/how_does_pip_install_e_work_is_there_a_specific/



## setuptool vs distutil

> The original Python packaging system, added to the standard library in Python 2.0.
>
> Due to the challenges of maintaining a packaging system where feature updates are tightly coupled to language runtime updates, direct usage of distutils is now actively discouraged, with setuptools being the preferred replacement. setuptools not only provides features that plain distutils doesn’t offer (such as dependency declarations and entry point declarations), it also provides a consistent build interface and feature set across all supported Python versions.