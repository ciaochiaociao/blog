---
title: Packaging and Distributing Python Libraries/Modules/Packages
---

ref:

- main: 
  - https://setuptools.readthedocs.io/en/latest/
  - https://packaging.python.org/
- walkthroughs: 
  - https://packaging.python.org/guides/ (to finish)
  - https://setuptools.readthedocs.io/en/latest/userguide/index.html
- tldr: https://packaging.python.org/tutorials/packaging-projects/
- history: https://bernat.tech/posts/pep-517-518/ (to read)

## Cookbook

```bash
python setup.py sdist bdist_wheel
python setup.py install/build
python setup.py easy_install
python setup.py build_ext -i/--inplace
# uses the setup.py to build our extension modules. The --inplace flag moves the compiled shared objects into the source tree so that they are in the Python search path.
pip install -e  # https://www.reddit.com/r/learnpython/comments/ayx7za/how_does_pip_install_e_work_is_there_a_specific/
```

## Basics

```bash
python3 -m build  # same as python3 -m build --sdist --wheel
```

1. Standalone Python Module (`.py`)

2. [Python source distributions](https://packaging.python.org/overview/#id7) (`sdist`): 

   ```bash
   python3 -m build --sdist
   # vs ?
   python setup.py sdist
   ```

   - So long as your code contains nothing but pure Python code, and you know your deployment environment supports your version of Python, then you can use Python’s native packaging tools to create a *source* [Distribution Package](https://packaging.python.org/glossary/#term-Distribution-Package), or *sdist* for short.
   - Python’s *sdists* are compressed archives (`.tar.gz` files) containing one or more packages or modules. If your code is pure-Python, and you only depend on other Python packages, you can [go here to learn more](https://docs.python.org/3/distutils/sourcedist.html).
   - A “source distribution” is unbuilt (i.e. it’s not a [Built Distribution](https://packaging.python.org/glossary/#term-Built-Distribution)), and requires a build step when installed by pip. Even if the distribution is pure Python (i.e. contains no extensions), it still involves a build step to build out the installation metadata from `setup.py` and/or `setup.cfg`.

3. [Python binary distributions](https://packaging.python.org/overview/#id8) (`bdist_wheel`)

   ```bash
   python3 -m build --wheel
   # vs ?
   python setup.py bdist_wheel
   ```

   - A wheel is a [built package](https://packaging.python.org/glossary/#term-Built-Distribution) that can be installed without needing to go through the “build” process. 
   
      - If your project is pure Python then you’ll be creating a [“Pure Python Wheel” (see section below)](https://packaging.python.org/guides/distributing-packages-using-setuptools/#pure-python-wheels). *Pure Python Wheels* contain no compiled extensions, and therefore only require a single Python wheel. The `wheel` package will detect that the code is pure Python, and build a wheel that’s named such that it’s usable on any Python 3 installation.
      
      - If your project contains compiled extensions, then you’ll be creating what’s called a [*Platform Wheel* (see section below)](https://packaging.python.org/guides/distributing-packages-using-setuptools/#platform-wheels). The `wheel` package will detect that the code is not pure Python, and build a wheel that’s named such that it’s only usable on the platform that it was built on.


   - If you rely on any non-Python code, or non-Python packages (such as [libxml2](https://en.wikipedia.org/wiki/Libxml2) in the case of [lxml](https://pypi.org/project/lxml/), or BLAS libraries in the case of [numpy](https://pypi.org/project/numpy))

   - So much of Python’s practical power comes from its ability to integrate with the software ecosystem, in particular libraries written in C, C++, Fortran, Rust, and other languages.

   - Not all developers have the right tools or experiences to build these components written in these compiled languages, so Python created the [Wheel](https://packaging.python.org/glossary/#term-Wheel), a package format designed to ship libraries with compiled artifacts. 

   - In fact, Python’s package installer, `pip`, always prefers wheels because installation is always faster, so even pure-Python packages work better with wheels.

   - Installing wheels is substantially faster for the end user than installing from a source distribution.

   - Note

      If your project also supports Python 2 _and_ contains no C extensions, then you should create what’s called a *Universal Wheel* by adding the following to your `setup.cfg` file:

      ```toml
      [bdist_wheel]
      universal=1
      ```

      Only use this setting if your project does not have any C extesions _and_ supports Python 2 and 3.


Note: Binary distributions are best when they come with source distributions to match. Even if you don’t upload wheels of your code for every operating system, by uploading the sdist, you’re enabling users of other platforms to still build it for themselves. Default to publishing both sdist and wheel archives together, *unless* you’re creating artifacts for a very specific use case where you know the recipient only needs one or the other.

Python and PyPI make it easy to upload both wheels and sdists together.

```bash
python setup.py sdist bdist_wheel

dist/
  example_pkg_YOUR_USERNAME_HERE-0.0.1-py3-none-any.whl  # bdist_wheel
  example_pkg_YOUR_USERNAME_HERE-0.0.1.tar.gz  # sdist
```

![A summary of Python's packaging capabilities for tools and libraries.](../../assets/images/py_pkg_tools_and_libs.png)

![A summary of technologies used to package Python applications.](../../assets/images/py_pkg_applications.png)

## setuptool

ref: 
- https://stackoverflow.com/questions/19048732/python-setup-py-develop-vs-install
- https://setuptools.readthedocs.io/en/latest/setuptools.html#development-mode
- https://setuptools.readthedocs.io/en/latest/userguide/declarative_config.html#declarative-config
- https://setuptools.readthedocs.io/en/latest/build_meta.html#


- All python package must provide a `pyproject.toml` and specify the backend (build system) it wants to use.

```toml
# To use with setuptools
[build-system]
requires = ["setuptools", "wheel"]
build-backend = "setuptools.build_meta"
```

```python
# setup.py
from setuptools import setup

# optional
with open("README.md", "r") as fh:
    long_description = fh.read()


setup(
    name='mypackage',
    version='0.0.1',

    packages=['mypackage'],
    # or
    packages=setuptools.find_packages(),

    install_requires=[
        'requests',
        'importlib; python_version == "2.6"',
    ],
    
    # optional
    author="Example Author",
    author_email="author@example.com",
    description="A small example package",
    long_description=long_description,
    long_description_content_type="text/markdown",
    url="https://github.com/pypa/sampleproject",
    classifiers=[
        "Programming Language :: Python :: 3",
        "License :: OSI Approved :: MIT License",
        "Operating System :: OS Independent",
    ],
    python_requires='>=3.6',
)
```

```toml
# setup.cfg
[metadata]
name = meowpkg
version = 0.0.1
description = a package that meows

[options]
packages = meowpkg
#or
packages = find:
#or
packages = find_namespace:  # to include Namespace Packages: packages that do not have a direct __init__.py under it

package_dir =  # package_dir: {'': 'src'}
    =src  # or = src
install_requires =  # install_requires: ['requests', ...
    requests
    importlib; python_version == "2.6"

[options.packages.find] #optional
where = src  #optional. default is the current directory
include=pkg1, pkg2, some_prefix_*
exclude=pk3, pk4

[options.entry_points]  # https://setuptools.readthedocs.io/en/latest/userguide/entry_point.html
console_scripts =
    command-name = timmins:some_function
    # <name> = [<package>.[<subpackage>.]]<module>[:<object>.<object>]
gui_scripts = ...  # gui instead of terminal
```


```bash
~/mypackage/
    pyproject.toml  # optional and new. But highly recommended. 
    setup.cfg  # or setup.py
    mypackage/__init__.py
    LICENSE  # optional
    README.md  # optional
    dist  # after running python -m build and can be uploaded to PyPI
    	*.tar.gz
    	*.whl
```

```bash
# get a builder, e.g., PyPA build
pip install -q build
python -m build
```

```bash
# install
pip install dist/meowpkg-0.0.1.whl
# or
pip install dist/meowpkg-0.0.1.tar.gz
```

- `python setup.py install` is used to install (typically third party) packages that you're not going to develop/modify/debug yourself.
- The `develop` will not install the package but it will create a `.egg-link` in the deployment directory back to the project source code directory
- And, if your deployment directory is Python’s `site-packages` directory, it will also update the `easy-install.pth` file to include your project’s source code, thereby making it available on `sys.path` for all programs using that Python installation.
- Note that it is highly recommended to use `pip install .` (install) and `pip install -e .` (developer install) to install packages, as invoking `setup.py` directly will do the wrong things for many dependencies, such as pull prereleases and incompatible package versions, or make the package hard to uninstall with `pip`.

## All setup.py commands

```
Standard commands:
  build             build everything needed to install
  build_py          "build" pure Python modules (copy to build directory)
  build_ext         build C/C++ extensions (compile/link to build directory)
  build_clib        build C/C++ libraries used by Python extensions
  build_scripts     "build" scripts (copy and fixup #! line)
  clean             clean up temporary files from 'build' command
  install           install everything from build directory
  install_lib       install all Python modules (extensions and pure Python)
  install_headers   install C/C++ header files
  install_scripts   install scripts (Python or otherwise)
  install_data      install data files
  sdist             create a source distribution (tarball, zip file, etc.)
  register          register the distribution with the Python package index
  bdist             create a built (binary) distribution
  bdist_dumb        create a "dumb" built distribution
  bdist_rpm         create an RPM distribution
  bdist_wininst     create an executable installer for MS Windows
  check             perform some checks on the package
  upload            upload binary package to PyPI

Extra commands:
  alias             define a shortcut to invoke one or more commands
  
  bdist_egg         create an "egg" distribution
  bdist_wheel       create a wheel distribution
  develop           install package in 'development mode'
  easy_install      Find/get/install Python packages
  install_egg_info  Install an .egg-info directory for the package
  egg_info          create a distribution's .egg-info directory
  dist_info         create a .dist-info directory
  
  test              run unit tests after in-place build (deprecated)
  
  rotate            delete older distributions, keeping N newest files
  
  saveopts          save supplied options to setup.cfg or other config file
  setopt            set an option in setup.cfg or another config file
  
  upload_docs       Upload documentation to PyPI
  
  init_catalog      create a new catalog based on a POT file
  compile_catalog   compile message catalogs to binary MO files
  update_catalog    update message catalogs from a POT file
  
  extract_messages  extract localizable strings from the project code
```

## Uploading

> Warning
>
> In other resources you may encounter references to using `python setup.py register` and `python setup.py upload`. These methods of registering and uploading a package are **strongly discouraged** as it may use a plaintext HTTP or unverified HTTPS connection on some Python versions, allowing your username and password to be intercepted during transmission.
>
> Tip
>
> The reStructuredText parser used on PyPI is **not** Sphinx! Furthermore, to ensure safety of all users, certain kinds of URLs and directives are forbidden or stripped out (e.g., the `.. raw::` directive). **Before** trying to upload your distribution, you should check to see if your brief / long descriptions provided in `setup.py` are valid. You can do this by running [twine check](https://packaging.python.org/guides/) on your package files:

```bash
python3 -m pip install twine

# 0. Check if it is valid. (Optional)
twine check dist/*
twine upload --repository testpypi dist/*  # same as --repository-url https://test.pypi.org/legacy/ 
pip install --index-url https://test.pypi.org/simple/ --no-deps example-pkg-YOUR-USERNAME-HERE
python3 -c 'import example_pkg;'

# 1. Create an account

# 2. Go to https://pypi.org/manage/account/#api-tokens and create a new API token; don’t limit its scope to a particular project, since you are creating a new project. (Don’t close the page until you have copied and saved the token — you won’t see that token again.)

# 2b. Save token to $HOME/.pypirc file for convenience (Optional)
#   [pypi]
#   username = __token__
#   password = <the token value, including the `pypi-` prefix>

# 3. 
twine upload dist/*  

# 4. check on https://pypi.org/project/<sampleproject>
```

## Namespace Packages and Package Discovery

ref: https://setuptools.readthedocs.io/en/latest/userguide/package_discovery.html#package-discovery

```bash
foo/
    src/
        timmins/foo/__init__.py  # timmins do not have a direct __init__.py 
    setup.cfg # or setup.py

# in setup.cfg
# package_dir = =src
# packages = find:  # won't work
# packages = find_namespace:  # works

# Legacy Namespace Packages:
# First create src/timmins/__init__.py, then
#   1. pkg_resource style 
#     __import__("pkg_resources").declare_namespace(__name__)  # in __init__.py
#     [options]  # in setup.cfg
#     namespace_packages = timmins
#   2. pkgutil style
#     __path__ = __import__('pkgutil').extend_path(__path__, __name__)  # in __init__.py

```

## setuptool vs distutil

> The original Python packaging system, added to the standard library in Python 2.0.
>
> Due to the challenges of maintaining a packaging system where feature updates are tightly coupled to language runtime updates, direct usage of distutils is now actively discouraged, with setuptools being the preferred replacement. setuptools not only provides features that plain distutils doesn’t offer (such as dependency declarations and entry point declarations), it also provides a consistent build interface and feature set across all supported Python versions.

## setuptool Entrypoint

### Advertising Behavior

Console scripts are one use of the more general concept of entry points. Entry points more generally allow a packager to advertise behavior for discovery by other libraries and applications. This feature enables “plug-in”-like functionality, where one library solicits entry points and any number of other libraries provide those entry points.

A good example of this plug-in behavior can be seen in [pytest plugins](https://docs.pytest.org/en/latest/writing_plugins.html), where pytest is a test framework that allows other libraries to extend or modify its functionality through the `pytest11` entry point.

The console scripts work similarly, where libraries advertise their commands and tools like `pip` create wrapper scripts that invoke those commands.

For a project wishing to solicit entry points, Setuptools recommends the [importlib.metadata](https://docs.python.org/3/library/importlib.metadata.html) module (part of stdlib since Python 3.8) or its backport, [importlib_metadata](https://pypi.org/project/importlib_metadata).

For example, to find the console script entry points from the example above:

```
>>> from importlib import metadata
>>> eps = metadata.entry_points()['console_scripts']
```

`eps` is now a list of `EntryPoint` objects, one of which corresponds to the `hello-world = timmins:hello_world` defined above. Each `EntryPoint` contains the `name`, `group`, and `value`. It also supplies a `.load()` method to import and load that entry point (module or object).

```
[options.entry_points]
my.plugins =
    hello-world = timmins:hello_world
```

Then, a different project wishing to load ‘my.plugins’ plugins could run the following routine to load (and invoke) such plugins:

```
>>> from importlib import metadata
>>> eps = metadata.entry_points()['my.plugins']
>>> for ep in eps:
...     plugin = ep.load()
...     plugin()
```

The project soliciting the entry points needs not to have any dependency or prior knowledge about the libraries implementing the entry points, and downstream users are able to compose functionality by pulling together libraries implementing the entry points.

### Dependency Management

Some entry points may require additional dependencies to properly function. For such an entry point, declare in square brakets any number of dependency `extras` following the entry point definition. Such entry points will only be viable if their extras were declared and installed. See the [guide on dependencies management](https://setuptools.readthedocs.io/en/latest/userguide/dependency_management.html) for more information on defining extra requirements. Consider from the above example:

```
[options.entry_points]
console_scripts =
    hello-world = timmins:hello_world [pretty-printer]
```

In this case, the `hello-world` script is only viable if the `pretty-printer` extra is indicated, and so a plugin host might exclude that entry point (i.e. not install a console script) if the relevant extra dependencies are not installed.

## pip VS easy_install

[pip](https://packaging.python.org/key_projects/#pip) came later in 2008, as alternative to [easy_install](https://packaging.python.org/key_projects/#easy-install), although still largely built on top of [setuptools](https://packaging.python.org/key_projects/#setuptools) components. It was notable at the time for *not* installing packages as [Eggs](https://packaging.python.org/glossary/#term-Egg) or from [Eggs](https://packaging.python.org/glossary/#term-Egg) (but rather simply as ‘flat’ packages from [sdists](https://packaging.python.org/glossary/#term-Source-Distribution-or-sdist)), and introducing the idea of [Requirements Files](https://pip.pypa.io/en/latest/user_guide/#requirements-files), which gave users the power to easily replicate environments.

|                                                              | **pip**                                                      | **easy_install**                                             |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Installs from [Wheels](https://packaging.python.org/glossary/#term-Wheel) | Yes                                                          | No                                                           |
| Uninstall Packages                                           | Yes (`python -m pip uninstall`)                              | No                                                           |
| Dependency Overrides                                         | Yes ([Requirements Files](https://pip.pypa.io/en/latest/user_guide/#requirements-files)) | No                                                           |
| List Installed Packages                                      | Yes (`python -m pip list` and `python -m pip freeze`)        | No                                                           |
| [**PEP 438**](https://www.python.org/dev/peps/pep-0438) Support | Yes                                                          | No                                                           |
| Installation format                                          | ‘Flat’ packages with `egg-info` metadata.                    | Encapsulated Egg format                                      |
| sys.path modification                                        | No                                                           | Yes                                                          |
| Installs from [Eggs](https://packaging.python.org/glossary/#term-Egg) | No                                                           | Yes                                                          |
| [pylauncher support](https://bitbucket.org/vinay.sajip/pylauncher) | No                                                           | Yes [1](https://packaging.python.org/discussions/pip-vs-easy-install/#id3) |
| [Multi-version installs](https://packaging.python.org/guides/multi-version-installs/#multi-version-installs) | No                                                           | Yes                                                          |
| Exclude scripts during install                               | No                                                           | Yes                                                          |
| per project index                                            | Only in virtualenv                                           | Yes, via setup.cfg                                           |



## Wheel VS Egg

- [Wheel](https://packaging.python.org/glossary/#term-Wheel) has an [**official PEP**](https://www.python.org/dev/peps/pep-0427). [Egg](https://packaging.python.org/glossary/#term-Egg) did not.
- [Wheel](https://packaging.python.org/glossary/#term-Wheel) is a [distribution](https://packaging.python.org/glossary/#term-Distribution-Package) format, i.e a packaging format. [1](https://packaging.python.org/discussions/wheel-vs-egg/#id3) [Egg](https://packaging.python.org/glossary/#term-Egg) was both a distribution format and a runtime installation format (if left zipped), and was designed to be importable.
- [Wheel](https://packaging.python.org/glossary/#term-Wheel) archives do not include .pyc files. Therefore, when the distribution only contains Python files (i.e. no compiled extensions), and is compatible with Python 2 and 3, it’s possible for a wheel to be “universal”, similar to an [sdist](https://packaging.python.org/glossary/#term-Source-Distribution-or-sdist).
- [Wheel](https://packaging.python.org/glossary/#term-Wheel) uses [**PEP376-compliant**](https://www.python.org/dev/peps/pep-0376) `.dist-info` directories. Egg used `.egg-info`.
- [Wheel](https://packaging.python.org/glossary/#term-Wheel) has a [**richer file naming convention**](https://www.python.org/dev/peps/pep-0425). A single wheel archive can indicate its compatibility with a number of Python language versions and implementations, ABIs, and system architectures.
- [Wheel](https://packaging.python.org/glossary/#term-Wheel) is versioned. Every wheel file contains the version of the wheel specification and the implementation that packaged it.
- [Wheel](https://packaging.python.org/glossary/#term-Wheel) is internally organized by [sysconfig path type](http://docs.python.org/2/library/sysconfig.html#installation-paths), therefore making it easier to convert to other formats.

