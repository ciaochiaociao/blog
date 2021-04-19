---
layout: post
title: Python Documentation Generator and Hosting service - Restructured Text (reST) and Sphinx, and pydoc
---

ref: https://thomas-cokelaer.info/tutorials/sphinx/rest_syntax.html

```
# Python Documentation Generator and Hosting service

1. Sphinx, readthedocs.org / ***.readthedocs.io

​```bash
pip install Sphinx
cd <your-project>
mkdir docs
sphinx-quickstart  (select the sphinx's "project-root" and select 'separate' will create souce folder)
sphinx-apidocs -o docs/source <your-project>
make html  (call sphinx-build to build the files) (required to see locally, but no need in readthedocs)
​```

>in conf.py
​```python
# Path setup - uncomment the following lines: If extensions (or modules to document with autodoc) are in another directory,
# add these directories to sys.path here. If the directory is relative to the
# documentation root, use os.path.abspath to make it absolute, like shown here.
import os
import sys
sys.path.insert(0, os.path.abspath('../../'))  # the path to the project root (proj-root - docs - source - conf.py)

# "sphinx.ext.autodoc" to support auto documentation and "recommonmark" to support using
extensions = ["sphinx.ext.autodoc", "recommonmark"]  markdown in sphinx

# readthedocs theme
html_theme = 'sphinx_rtd_theme'

# add this line for supporting readthedocs to read the index.rst instead of master.rst
master_doc = 'index'
​```

(note: rst file stands for reStructuredText file,
"project root" in Sphinx means the "project root" where all the documentation files reside)

>in readthedocs.org
git push to your github repo
import the github project from your github account
it will automatically build the required html/css/js files for you
visit <project-name>.readthedocs.io

2. pydoc
pydoc -p 8888
```

