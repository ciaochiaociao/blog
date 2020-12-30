---
title: Python Setup / pip install / setup.py
---

ref: https://stackoverflow.com/questions/19048732/python-setup-py-develop-vs-install

ref: https://setuptools.readthedocs.io/en/latest/setuptools.html#development-mode

- `python setup.py install` is used to install (typically third party) packages that you're not going to develop/modify/debug yourself.
- The `develop` will not install the package but it will create a `.egg-link` in the deployment directory back to the project source code directory
-  And, if your deployment directory is Python’s `site-packages` directory, it will also update the `easy-install.pth` file to include your project’s source code, thereby making it available on `sys.path` for all programs using that Python installation.
- Note that it is highly recommended to use `pip install .` (install) and `pip install -e .` (developer install) to install packages, as invoking `setup.py` directly will do the wrong things for many dependencies, such as pull prereleases and incompatible package versions, or make the package hard to uninstall with `pip`.