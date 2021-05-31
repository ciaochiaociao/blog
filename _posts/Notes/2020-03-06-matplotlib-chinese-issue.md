---
title: 解決Matplotlib 中文顯示問題
---

ref: https://medium.com/marketingdatascience/%E8%A7%A3%E6%B1%BApython-3-matplotlib%E8%88%87seaborn%E8%A6%96%E8%A6%BA%E5%8C%96%E5%A5%97%E4%BB%B6%E4%B8%AD%E6%96%87%E9%A1%AF%E7%A4%BA%E5%95%8F%E9%A1%8C-f7b3773a889b

```python
# 1. get font dir and default font
from matplotlib.font_manager import findfont, FontProperties
findfont(FontProperties(family=FontProperties().get_family()))
# '/home/chiao/anaconda3/lib/python3.7/site-packages/matplotlib/mpl-data/fonts/ttf/DejaVuSans.ttf'

# 2. download 中文 ttf font to the folder above
# e.g., 微軟正黑體 from https://korenetmedia.com/download/%E5%BE%AE%E8%BB%9F%E6%AD%A3%E9%BB%91%E9%AB%94ttf%E5%AD%97%E5%9E%8B/#
# move <font>.ttf to <matplotlib_font_folder> (/home/chiao/anaconda3/lib/python3.7/site-packages/matplotlib/mpl-data/fonts/)

# 3. delete ~/.cache/matplotlib (required)
rm -r ~/.cache/matplotlib

# 4. add font to settings (check font name with https://fontdrop.info/)
# Option A: in matplotlibrc (4 locations. Recommended to use ~/.config/matplotlib/matplotlibrc)
font.sans-serif: <font_name> (e.g. Microsoft JhengHei), ...  # uncomment this line and add the font 

# Option B: dynamically in python
from matplotlib.font_manager import FontProperties

import matplotlib.pyplot as plt
plt.rcParams['font.sans-serif'] = ['Microsoft JhengHei'] 
plt.rcParams['axes.unicode_minus'] = False

import seaborn as sns
myfont=FontProperties(fname=r'C:\Users\howar\Desktop\msj.ttf',size=14)
sns.set(font=myfont.get_family())
sns.set_style("whitegrid",{"font.sans-serif":['Microsoft JhengHei']})
```