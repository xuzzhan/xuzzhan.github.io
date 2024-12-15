---
# layout: post
# layout: single
title: "Geopandas plotting settings"
# tags:
#  - table of contents
toc: true
toc_sticky: true
---

对地理空间数据的可视化，自己经常使用Geopandas这个Python库，但零零散散地写，经常想设置一些具体绘图细节时每次都要从杂乱代码山中去找。今天想要修改绘图颜色条的文字标签，又是找了半天。索性趁此机会总结一下Geopandas库的绘图代码，包含自己曾经写过的、文档和论坛中看到的。参考链接置于该文最后，有需要请自行查阅。

```python
```python
import geopandas as gpd
gdf = gpd.read_file()

### 基本绘图代码
# 这是gpd绘图的最基本函数，所有参数均在函数说明中已经提供
gdf.plot(column, legend, cmap, scheme, legend_kwds)

### 修改图例细节
# 由于自己需求主要是对图例修改，所以主要先介绍该部分代码

# 不设置schemed的colorbar设置
gdf.plot(column, 
         legend=True, 
         vmax, vmin,
         legend_kwds={'shrink':0.5,  # colorbar缩放
                      'loc':lower left,  # location
                      'ticks':vmin, vmax,
                      'format':mticker.FixedFormatter(['Low','High'])})  # 设置对应ticks的文字标签

# 也可以从该图中读取colorbar单独设置
ax = gdf.plot(column, legend=True)
cbr = fig.axes[1] 
cbr.tick_params(labelsize)  # 修改cbr的字体大小

# 还可以自己创建一个colorbar
vmin = gdf[column].min()
vmax = gdf[column].max()
cax = fig.add_axes([0.55, 0.14, 0.4, 0.02])  # 左侧边界、底部边界、宽度和高度
sm = plt.cm.ScalarMappable(cmap, norm=plt.Normalize(vmin=vmin, vmax=vmax))
cbr = fig.colorbar(sm, cax=cax, orientation='horizontal')
cbr.ax.tick_params(labelsize)  # 设置标签文本字体大小
cbr.outline.set_linewidth(0)  # 设置cbr的边框宽度

# 设置schemed的图例设置
ax = gdf.plot(column, scheme='Naturalbreaks', legend=True)
leg1 = ax.get_legend()
for ea in leg1.legendHandles:
    ea.set_marker('s')  # 图形形状
    ea.set_markeredgecolor('k')  # 边颜色
    ea.set_markeredgewidth(0.5)  # 边线宽
    
# 也可以自行导入线、矩形作图例
import matplotlib.lines as mlines
import matplotlib.patches as mpatches

ax = gdf.plot(column, scheme='Naturalbreaks', k=5, legend=True)
labels = [t.get_text() for t in ax.get_legend().get_texts()]  # 获取图例中的文本
LegendElement = [mlines.Line2D([0],[0],lw,color,label) for i in range(5)]  # 为文本设置线形状、线宽和颜色，5是分类数量k
legend = ax.legend(handles=LegendElement, loc, title, fontsize, title_fontsize, frameon, shadow)
legend.get_title().set_fontweight('bold')  # 标题设置字体粗度
```

参考：

https://gis.stackexchange.com/questions/378416/how-to-change-the-font-size-of-the-color-bar-of-a-geopandas-choropleth-plot

https://www.5axxw.com/questions/content/nmrngc