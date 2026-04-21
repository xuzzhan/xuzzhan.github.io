---
title: "Generating polygon boundary from line data"
layout: single
categories: Code
---

{% include code-header.html %}

对线要素进行聚合生成面单元，以获取边界。可以通过两种方法实现：凸包和凹包。
代码如下。

```python
# 【方法 A：凹包 Concave Hull】
# ratio: 控制凹陷程度，0 相当于凸包，1 相当于最紧凑的边界
# allow_holes=False: 强制忽略内部的中空区域，直接生成完整的多边形
boundary_concave = merged_gdf.copy()
boundary_concave.geometry = boundary_concave.geometry.concave_hull(ratio=0.3, allow_holes=False)

# 【方法 B：凸包 Convex Hull】
# 如果凹包的边缘太破碎，可以用凸包获取一个绝对完整的外部轮廓
boundary_convex = merged_gdf.copy()
boundary_convex.geometry = boundary_convex.geometry.convex_hull

fig, ax = plt.subplots(1,2, figsize=(16, 8))
boundary_concave.plot('com', ax=ax[0], cmap='Pastel1', alpha=0.5, edgecolor='black')
merged_gdf.plot('com', ax=ax[0], linewidth=1)
boundary_convex.plot('com', ax=ax[1], cmap='Pastel1', alpha=0.5, edgecolor='black')
merged_gdf.plot('com', ax=ax[1], linewidth=1)
plt.show()
```

两种方式的结果图对比。
<figure style="width: 35%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/post_figs/line-to-polygon-boundary/fig1.png" alt="">
  <!-- <figcaption>Itty-bitty caption.</figcaption> -->
</figure> 