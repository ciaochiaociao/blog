---
title: Packaging and Distributing Python Libraries/Modules/Packages
---

1. Standalone Python Module (`.py`)

2. [Python source distributions](https://packaging.python.org/overview/#id7) (`sdist`): So long as your code contains nothing but pure Python code, and you know your deployment environment supports your version of Python, then you can use Python’s native packaging tools to create a *source* [Distribution Package](https://packaging.python.org/glossary/#term-Distribution-Package), or *sdist* for short.

   Python’s *sdists* are compressed archives (`.tar.gz` files) containing one or more packages or modules. If your code is pure-Python, and you only depend on other Python packages, you can [go here to learn more](https://docs.python.org/3/distutils/sourcedist.html).

3. [Python binary distributions](https://packaging.python.org/overview/#id8) (`bdist_wheel`)

   If you rely on any non-Python code, or non-Python packages (such as [libxml2](https://en.wikipedia.org/wiki/Libxml2) in the case of [lxml](https://pypi.org/project/lxml/), or BLAS libraries in the case of [numpy](https://pypi.org/project/numpy))

   So much of Python’s practical power comes from its ability to integrate with the software ecosystem, in particular libraries written in C, C++, Fortran, Rust, and other languages.

   Not all developers have the right tools or experiences to build these components written in these compiled languages, so Python created the [Wheel](https://packaging.python.org/glossary/#term-Wheel), a package format designed to ship libraries with compiled artifacts. In fact, Python’s package installer, `pip`, always prefers wheels because installation is always faster, so even pure-Python packages work better with wheels.

Note: Binary distributions are best when they come with source distributions to match. Even if you don’t upload wheels of your code for every operating system, by uploading the sdist, you’re enabling users of other platforms to still build it for themselves. Default to publishing both sdist and wheel archives together, *unless* you’re creating artifacts for a very specific use case where you know the recipient only needs one or the other.

Python and PyPI make it easy to upload both wheels and sdists together.

```
python3 setup.py sdist bdist_wheel

dist/
  example_pkg_YOUR_USERNAME_HERE-0.0.1-py3-none-any.whl  # bdist_wheel
  example_pkg_YOUR_USERNAME_HERE-0.0.1.tar.gz  # sdist
```



[![A summary of Python's packaging capabilities for tools and libraries.](https://packaging.python.org/_images/py_pkg_tools_and_libs.png)](https://packaging.python.org/_images/py_pkg_tools_and_libs.png)