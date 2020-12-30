---
title: Use ipywidgets in Jupyter Lab
---

ref: https://github.com/nodesource/distributions/blob/master/README.md

!curl -sL https://deb.nodesource.com/setup_12.x | bash -
!apt-get install -y nodejs

!pip install ipywidgets
!jupyter nbextension enable --py widgetsnbextension

!jupyter labextension install @jupyter-widgets/jupyterlab-manager
(for ipywidgets to work in jupyter lab)