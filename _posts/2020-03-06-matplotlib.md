---
title: 解決Matplotlib 中文顯示問題
---

ref: https://medium.com/marketingdatascience/%E8%A7%A3%E6%B1%BApython-3-matplotlib%E8%88%87seaborn%E8%A6%96%E8%A6%BA%E5%8C%96%E5%A5%97%E4%BB%B6%E4%B8%AD%E6%96%87%E9%A1%AF%E7%A4%BA%E5%95%8F%E9%A1%8C-f7b3773a889b

```
# 1. get font dir and default font
from matplotlib.font_manager import findfont, FontProperties
findfont(FontProperties(family=FontProperties().get_family()))
# '/home/chiao/anaconda3/lib/python3.7/site-packages/matplotlib/mpl-data/fonts/ttf/DejaVuSans.ttf'

#2. in cmd
cd 
wget -O /home/chiao/anaconda3/lib/python3.7/site-packages/matplotlib/mpl-data/matplotlibrc http://cloud.ziti8.cn/fonts/weiruan/%E5%BE%AE%E8%BD%AF%E6%AD%A3%E9%BB%91%E4%BD%93.ttf
mv 微软正黑体.ttf msj.ttf

#3. in matplotlibrc
font.family: sans-serif
font.serif: Microsoft JhengHei, ...

#4. delete ~/.cache/matplotlib
rm -r ~/.cache/matplotlib

# in python
#5.1 plt
import matplotlib.pyplot as plt

from matplotlib.font_manager import FontProperties
plt.rcParams['font.sans-serif'] = ['Microsoft JhengHei'] 
plt.rcParams['axes.unicode_minus'] = False

#5.2 sns
import seaborn as sns

from matplotlib.font_manager import FontProperties
myfont=FontProperties(fname=r'C:\Users\howar\Desktop\msj.ttf',size=14)
sns.set(font=myfont.get_family())
sns.set_style("whitegrid",{"font.sans-serif":['Microsoft JhengHei']})
```