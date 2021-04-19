---
title: Python Plot
---

```python
## matplotlib

### confusion matrix 1: only using matplotlib
import matplotlib.pyplot as plt

fig = plt.figure()
ax = fig.add_subplot(111)
cax = ax.matshow(cm)

labels = ["person", "location", "corporation", "group", "creative-work", "product"]

plt.title('Confusion matrix of the classifier')
# fig.colorbar(cax)

ax.set_xticklabels([''] + labels)
ax.set_yticklabels([''] + labels)
ax.xaxis.tick_bottom()
plt.xticks(rotation=45, ha='right', rotation_mode='anchor')

plt.xlabel('Predicted')
plt.ylabel('True')

# Loop over data dimensions and create text annotations.
for i in range(len(labels)):
    for j in range(len(labels)):
        text = ax.text(j, i, cm[i, j], ha="center", va="center", color="w")
        
plt.show()

### confusion matrix 2: seaborn, panda and plt
import seaborn as sn
import pandas as pd
import matplotlib.pyplot as plt

df_cm = pd.DataFrame(cm, index=labels, columns=labels)
plt.figure(figsize=(10,7))
sn.heatmap(df_cm, annot=True)


### confusion matrix 3: pretty print confusion matrix (similar to method 2)
#### from matrix
import pandas as pd
from confusion_matrix_pretty_print import pretty_plot_confusion_matrix

df_cm = pd.DataFrame(cm, index=labels, columns=labels)
pretty_plot_confusion_matrix(df_cm, pred_val_axis='x', show_null_values=0)

#### from two arrays
from confusion_matrix_pretty_print import plot_confusion_matrix_from_data
plot_confusion_matrix_from_data(y_true, y_pred, columns=labels)


### histogram
%matplotlib inline
import matplotlib.pyplot as plt
from collections import OrderedDict

ntype_od = OrderedDict(types_and_num)
plt.figure(figsize=(5,10))
plt.xticks(rotation=80)
plt.tick_params(labelsize=20)
plt.ylabel('Frequency', fontsize=30)
plt.labelsize = 20
plt.bar(ntype_od.keys(), ntype_od.values())
plt.savefig('types.png')

## Seaborn
​```
sns.pairplot(dframe, vars=[‘x0’, ‘x1’, ‘x2’, ‘x3’], hue=’y’)
​```

### separate total row and column (subplot)
plt.figure(figsize=(20, 20))
ax1 = plt.subplot2grid((20, 20), (1, 0), colspan=19, rowspan=19)
ax2 = plt.subplot2grid((20, 20), (0, 0), colspan=19, rowspan=1)
ax3 = plt.subplot2grid((20, 20), (1, 19), colspan=1, rowspan=19)

# sns.set(font_scale=1.2)
cmap = sns.cubehelix_palette(as_cmap=True, light=.9)
mask = df.isnull()
hm = sns.heatmap(df, ax=ax1, annot=True, annot_kws={"size": 25}, mask=mask, cbar=False, cmap=cmap, linewidths=2, fmt=".0f")
hm.set_facecolor(np.append(cmap.colors[0][:-1], 0.5))
hm.set_xticklabels(hm.get_xticklabels(), rotation=45, fontsize=18)
hm.set_yticklabels(hm.get_yticklabels(), rotation=45, fontsize=18)

sns.heatmap((pd.DataFrame(df.sum(axis=0))).transpose(), ax=ax2, annot=True, cmap=cmap, cbar=False,
            xticklabels=False, yticklabels=False, annot_kws={"size": 25})
sns.heatmap(pd.DataFrame(df.sum(axis=1)), ax=ax3, annot=True, cmap=cmap, cbar=False, xticklabels=False,
            yticklabels=False, annot_kws={"size": 25})


```

