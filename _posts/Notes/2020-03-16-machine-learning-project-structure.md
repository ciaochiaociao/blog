---
title: Machine-Learning-Project-Structure
---

Refs: 
- Overcome the Chaos
	- https://towardsdatascience.com/structure-and-automated-workflow-for-a-machine-learning-project-2fa30d661c1e
	- https://towardsdatascience.com/structure-and-automated-workflow-for-a-machine-learning-project-part-2-b5b420625102
	- https://github.com/artofai/overcome-the-chaos
- Building package for machine learning project in Python https://towardsdatascience.com/building-package-for-machine-learning-project-in-python-3fc16f541693
- How to Structure a Python-Based Data Science Project (a short tutorial for beginners) https://medium.com/swlh/how-to-structure-a-python-based-data-science-project-a-short-tutorial-for-beginners-7e00bff14f56
- Cookiecutter Data Science https://drivendata.github.io/cookiecutter-data-science/

Key points:
- Repeatable, Reproducible
	Including:
	- Downloading Data
	- Processing Data
	- Cleaning Data
	- Training
	- Predicting

## Project Structure:
### Data
```
data
├── external
├── interim
├── processed
└── raw
src
└── data
							└── download.py
```
![](https://miro.medium.com/max/1042/0*no6lYIk9qqcb0kSw.png)
![](https://miro.medium.com/max/1042/0*5loU1aNWoQTTX3_3.png)

## Use `Make` to autmoate the workflow
```
.PHONY: all clean  # targets that do not create files, should be marked as .PHONY

DATA_URL = "url/to/data"

all: data/raw/data.csv  # When you type just make it will execute first target. So it is reasonable to make all first target in file

clean:
 rm -f data/raw/*.csv

data/raw/data.csv:
  python src/data/download.py $(DATA_URL) $@  # $@ means “first target in a rule”

models/random_forest.model: data/processed/processed.pickle
	python src/models/train_model.py $< $@  # $< means “first requirement in a rule”
	
```
## Wrap `.py` file as a script and add arguments
You can use `click` to easily add arguments to a funciton in python

```
import requests
import click
@click.command()
@click.argument('url')
@click.argument('filename', type=click.Path())
def download_file(url, filename):
   print('Downloading from {} to {}'.format(url, filename))
   response = requests.get(url)
   with open(filename,  'wb') as ofile:
       ofile.write(response.content)
if __name__ == '__main__':
   download_file()
```

## Tools
- https://github.com/vpj/lab