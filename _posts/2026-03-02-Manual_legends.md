---
title: "Manually creating plot legends"
layout: single
categories: Code
---

{% include code-header.html %}

```python
import contextily as ctx
import numpy as np
from shapely.geometry import LineString
import matplotlib.lines as mlines
import matplotlib.patches as mpatches
import matplotlib.pyplot as plt
plt.rcParams['font.sans-serif'] = ['Songti SC']  # 设置中文字体为黑体
plt.rcParams['axes.unicode_minus'] = False  # 解决负号'-'显示为方块的问题

LegendElement = [
    mpatches.Patch(
        facecolor=colors[i],
        edgecolor="grey",
        label="label"
    ) 
    for i in range(k)
]
legend1 = ax.legend(
    handles=LegendElement,
    loc="upper left",
    title="title",
    title_fontsize=14,
    fontsize=14,
    frameon=False,
    handletextpad=0.2,
    ncol=2,
    # bbox_to_anchor=(0, 0.7),
)
legend1._legend_box.align = "left"
ax.add_artist(legend1)

LegendElement = [
    mlines.Line2D(
        [0], [0], 
        marker='o',          # 圆点标记
        linestyle='',        # 去掉线条
        markersize=2*i+2,       # 圆点大小（可调整）
        color="grey",
        label="label"
    ) 
    for i in range(k)
]
legend2 = ax.legend(
    handles=LegendElement,
    loc="lower left",
    title="title",
    title_fontsize=14,
    fontsize=14,
    frameon=False,
    handletextpad=0.2,
    bbox_to_anchor=(0, 0.38),
)
legend2._legend_box.align = "left"
ax.add_artist(legend2)

colors = plt.get_cmap('YlOrRd')(np.linspace(0.2, 1, 5))
LegendElement = [mlines.Line2D([0], [0], lw=i+1.2, color=colors[i], label="label") for i in range(5)]
legend2 = ax.legend(
    handles=LegendElement,
    loc="lower left",
    title="title",
    title_fontsize=14,
    fontsize=14,
    frameon=False,
    handletextpad=0.2,
    bbox_to_anchor=(0, 0),
)
ctx.add_basemap(
    ax,
    source='https://d.basemaps.cartocdn.com/light_nolabels/{z}/{x}/{y}.png',
    zorder=0,
)
ax.set_xlim(1.26*1e7, 1.343*1e7)
ax.set_ylim(3.46*1e6, 4.073*1e6)

ax.set_title("", fontsize=14, loc='left')
```
