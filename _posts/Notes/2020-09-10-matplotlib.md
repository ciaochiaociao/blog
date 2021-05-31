---
title: matplotlib
---

## Matplotlib

```
import matplotlib.pyplot as plt

plt.figure(figsize=(20,10))
fig, ax = plt.subplots(figsize=(20, 10))
plt.setp()
plt.title()
plt.xticks()
plt.tick_params()
plt.xlabels()
plt.axis()
plt.set_xlim()
plt.margins(0, 0)

plt.Circle((0, 0), 0.70, fc='white')

plt.subplots()
plt.gcf()
plt.tight_layout()
plt.figure()
plt.pause(5)
plt.show()
plt.savefig()
```

### Plot kinds

```
from matplotlib.sankey import Sankey
Sankey(flows=[0.25, 0.15, 0.60, -0.20, -0.15, -0.05, -0.50, -0.10], labels=['', '', '', 'First', 'Second', 'Third', 'Fourth', 'Fifth'], orientations=[-1, 1, 0, 1, 1, 1, 0,-1]).finish()
```

### Basic

- All of plotting functions expect [`numpy.array`](https://numpy.org/doc/stable/reference/generated/numpy.array.html#numpy.array) or [`numpy.ma.masked_array`](https://numpy.org/doc/stable/reference/generated/numpy.ma.masked_array.html#numpy.ma.masked_array) as input.

- Classes that are 'array-like' such as [`pandas`](https://pandas.pydata.org/pandas-docs/stable/index.html#module-pandas) data objects and [`numpy.matrix`](https://numpy.org/doc/stable/reference/generated/numpy.matrix.html#numpy.matrix) may or may not work as intended. 

- It is best to convert these to [`numpy.array`](https://numpy.org/doc/stable/reference/generated/numpy.array.html#numpy.array) objects prior to plotting.

  ```
  a = pandas.DataFrame(np.random.rand(4, 5), columns = list('abcde'))
  a_asarray = a.values
  
  b = np.matrix([[1, 2], [3, 4]])
  b_asarray = np.asarray(b)
  ```

![../../_images/anatomy.png](https://matplotlib.org/3.3.0/_images/anatomy.png)

ref: https://matplotlib.org/3.3.0/tutorials/introductory/usage.html#sphx-glr-tutorials-introductory-usage-py

### Three Ways

#### OO Interface (non-interactive, explicitly create Axes) (suggested)

> Explicitly create figures and axes, and call methods on them

Matplotlib graphs your data on [`Figure`](https://matplotlib.org/3.3.0/api/_as_gen/matplotlib.figure.Figure.html#matplotlib.figure.Figure)s (i.e., windows, Jupyter widgets, etc.), each of which can contain one or more [`Axes`](https://matplotlib.org/3.3.0/api/axes_api.html#matplotlib.axes.Axes) (i.e., an area where points can be specified in terms of x-y coordinates (or theta-r in a polar plot, or x-y-z in a 3D plot, etc.). The most simple way of creating a figure with an axes is using [`pyplot.subplots`](https://matplotlib.org/3.3.0/api/_as_gen/matplotlib.pyplot.subplots.html#matplotlib.pyplot.subplots).

```
fig, ax = plt.subplots()  # Create a figure containing a single axes.
ax.plot([1, 2, 3, 4], [1, 4, 2, 3])  # Plot some data on the axes.
```

#### pyplot Interface (interactive, implicitly) (Jupyter)

> Rely on pyplot to automatically create and manage the figures and axes, and use pyplot functions for plotting.

there is a corresponding function in the [`matplotlib.pyplot`](https://matplotlib.org/3.3.0/api/_as_gen/matplotlib.pyplot.html#module-matplotlib.pyplot) module that performs that plot on the "current" axes, creating that axes (and its parent figure) if they don't exist yet. So the previous example can be written more shortly as

```
# figure = plt.figure(figsize=(9,7), dpi=100) 
plt.plot([1, 2, 3, 4], [1, 4, 2, 3])  # Matplotlib plot.

# return figure
```

#### Embed Matplotlib in GUI 

 completely drops pyplot, even for figure creation

### Figure

- The **whole** figure. 
- The figure keeps track of all the child [`Axes`](https://matplotlib.org/3.3.0/api/axes_api.html#matplotlib.axes.Axes), a smattering of 'special' artists (titles, figure legends, etc), and the **canvas**.
- A figure can contain any number of [`Axes`](https://matplotlib.org/3.3.0/api/axes_api.html#matplotlib.axes.Axes), but will typically have at least one.

```
fig = plt.figure()  # an empty figure with no Axes
fig, ax = plt.subplots()  # a figure with a single Axes
fig, axs = plt.subplots(2, 2)  # a figure with a 2x2 grid of Axes
```

### Axis

These are the number-line-like objects. They take care of setting the graph limits and generating the ticks (the marks on the axis) and ticklabels (strings labeling the ticks). The location of the ticks is determined by a [`Locator`](https://matplotlib.org/3.3.0/api/ticker_api.html#matplotlib.ticker.Locator) object and the ticklabel strings are formatted by a [`Formatter`](https://matplotlib.org/3.3.0/api/ticker_api.html#matplotlib.ticker.Formatter). The combination of the correct [`Locator`](https://matplotlib.org/3.3.0/api/ticker_api.html#matplotlib.ticker.Locator) and [`Formatter`](https://matplotlib.org/3.3.0/api/ticker_api.html#matplotlib.ticker.Formatter) gives very fine control over the tick locations and labels.

### Artists

-  it is the object that actually does the drawing to get you your plot, but as the user it is more-or-less invisible to you
- Basically everything you can see on the figure is an artist, e.g.,  [`Figure`](https://matplotlib.org/3.3.0/api/_as_gen/matplotlib.figure.Figure.html#matplotlib.figure.Figure), [`Axes`](https://matplotlib.org/3.3.0/api/axes_api.html#matplotlib.axes.Axes), [`Axis`](https://matplotlib.org/3.3.0/api/axis_api.html#matplotlib.axis.Axis), [`Text`](https://matplotlib.org/3.3.0/api/text_api.html#matplotlib.text.Text) objects, [`Line2D`](https://matplotlib.org/3.3.0/api/_as_gen/matplotlib.lines.Line2D.html#matplotlib.lines.Line2D) objects, [`collections`](https://matplotlib.org/3.3.0/api/collections_api.html#module-matplotlib.collections) objects, [`Patch`](https://matplotlib.org/3.3.0/api/_as_gen/matplotlib.patches.Patch.html#matplotlib.patches.Patch) objects 
- When the figure is rendered, all of the artists are drawn to the **canvas**. 
- Most Artists are tied to an Axes; such an Artist cannot be shared by multiple Axes, or moved from one to another.

### Others

An ndarray is returned with one [`matplotlib.axes.Axes`](https://matplotlib.org/api/axes_api.html#matplotlib.axes.Axes) per column when `subplots=True`.

## matplotlibrc

Matplotlib uses `matplotlibrc` configuration files to customize all kinds of properties, which we call 'rc settings' or 'rc parameters'. You can control the defaults of almost every property in Matplotlib: figure size and DPI, line width, color and style, axes, axis and grid properties, text and font properties and so on. When a URL or path is not specified with a call to `style.use('<path>/<style-name>.mplstyle')`, Matplotlib looks for `matplotlibrc` in four locations, in the following order:

1. `matplotlibrc` in the current working directory, usually used for specific customizations that you do not want to apply elsewhere.

2. `$MATPLOTLIBRC` if it is a file, else `$MATPLOTLIBRC/matplotlibrc`.

3. It next looks in a user-specific place, depending on your platform:

   - On Linux and FreeBSD, it looks in `.config/matplotlib/matplotlibrc` (or `$XDG_CONFIG_HOME/matplotlib/matplotlibrc`) if you've customized your environment.
   - On other platforms, it looks in `.matplotlib/matplotlibrc`.

   See [matplotlib configuration and cache directory locations](https://matplotlib.org/stable/faq/troubleshooting_faq.html#locating-matplotlib-config-dir).

4. `*INSTALL*/matplotlib/mpl-data/matplotlibrc`, where `*INSTALL*` is something like `/usr/lib/python3.7/site-packages` on Linux, and maybe `C:\Python37\Lib\site-packages` on Windows. Every time you install matplotlib, this file will be overwritten, so if you want your customizations to be saved, please move this file to your user-specific matplotlib directory.

```
import matplotlib
matplotlib.matplotlib_fname()
```

