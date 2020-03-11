---
title: Use jupyter lab extension in WSL
---

# Use jupyter lab extension in WSL
- Using WSL 1 cannot run when `jupyter labextension install @jupyter-widgets/jupyterlab-manager`
```
PermissionError: [Errno 13] Permission denied: '/usr/local/share/jupyter/lab/extensions/jupyter-widgets-jupyterlab-manager-2.0.0.tgz'
```
- Using WSL 1 cannot recognize NodeJS when `sudo jupyter labextension install @jupyter-widgets/jupyterlab-manager` with sudo priviledge
```
ValueError: Please install nodejs 5+ and npm before continuing installation. nodejs may be installed using conda or directly from the nodejs website.
```
- **Solution**
	1. install Conda on WSL
	2. `conda install nodejs`
	3. ` jupyter labextension install @jupyter-widgets/jupyterlab-manager`