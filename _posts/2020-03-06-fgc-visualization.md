---
title: FGC Visualization
---

dockerfile
```
FROM jupyter/datascience-notebook

RUN sed -i 's/#font.family/font.family/g' /opt/conda/lib/python3.7/site-packages/matplotlib/mpl-data/matplotlibrc && \
	sed -i 's/font.serif          : /font.serif          : Microsoft JhengHei, /g' /opt/conda/lib/python3.7/site-packages/matplotlib/mpl-data/matplotlibrc && \
	wget -O /opt/conda/lib/python3.7/site-packages/matplotlib/mpl-data/fonts/ttf/msj.ttf \
http://cloud.ziti8.cn/fonts/weiruan/%E5%BE%AE%E8%BD%AF%E6%AD%A3%E9%BB%91%E4%BD%93.ttf && \
	pip install appmode && \
	jupyter nbextension enable --py --sys-prefix appmode && \
	jupyter serverextension enable --py --sys-prefix appmode

USER root

RUN useradd -u 1004 -G root cwhsu

USER cwhsu
WORKDIR /workspace/fgc_visual

#RUN pip install -r requirements.txt

CMD jupyter notebook --ip 0.0.0.0
```


docker-compose.yml
```
version: '3.7'

services:
  corenlp_zh:
    image: ciaochiaociao/corenlp_zh:0.2
    container_name: corenlp_zh
    ports:
    - 9001:9000
    command: java -mx8g -cp '*' edu.stanford.nlp.pipeline.StanfordCoreNLPServer -port 9000 -timeout 15000 -serverProperties StanfordCoreNLP-chinese.properties

  fgc_visual:
    image: ciaochiaociao/datascience-notebook:fix_zh
    ports:
      - 8765:8888
    container_name: fgc_visual
    volumes:
    - /home/cwhsu/workspace:/workspace
    working_dir: /workspace/fgc_visual
    command: jupyter notebook --ip 0.0.0.0
```