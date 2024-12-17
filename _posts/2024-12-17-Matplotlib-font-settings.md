---
title: "Matplotlib.pyplot font settings"
# toc: true
# toc_sticky: true
---

# 中文字体设置

最简单的方法是，在绘图之前加入这几行代码就可以显示中文字体。

```python
plt.rcParams["font.family"] = "serif"
plt.rcParams["font.serif"] = ["Microsoft YaHei"]
plt.rcParams["axes.unicode_minus"] = False #该语句解决图像中的“-”负号的乱码问题
```

如果想要全局修改更多字体参数，需要用这几行代码，但可以根据需求设置。

```python
config = {
    "font.family":'serif',
    "font.size": 16,
    "mathtext.fontset":'stix',
    "font.serif": ['STSong'],
    "axes.unicode_minus": False
}
plt.rcParams.update(config)
```

不同中文字体的设置参考（来源于参考2）

| 字体名称 |      别名       |
| :------: | :-------------: |
|   宋体   |     SimSun      |
|   黑体   |     Simhei      |
|   楷体   |      KaiTi      |
|   等线   |    DengXian     |
|   仿宋   |    FangSong     |
| 微软雅黑 | Microsoft YaHei |
| 华文宋体 |     STSong      |
| 华文中宋 |   STZhongsong   |
| 华文楷体 |     STKaiti     |
| 方正舒体 |     FZShuTi     |
| 华文新魏 |    STXinwei     |
| 方正姚体 |     FZYaoTi     |

# 中英字体混合显示

另一个问题是可能需要同时显示中文和英文字体，这个时候要用到matplotlib在2022年9月更新的新功能，具体可以查阅官方文档：https://matplotlib.org/stable/users/prev_whats_new/whats_new_3.6.0.html#fonts-and-text。

简单而言，就是设置两类字体（前英文，后中文）。

```python
plt.rcParams["font.family"] = ["Times New Roman", "SimSun"]
```

例如，在这里我设置标准的中文论文字体格式，英文用Times New Roman，中文用宋体。

<figure class="align-center">
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/post_figs/matplotlib-font-settings/test.jpg" width="30%" />
</figure> 


参考：

1. https://hscyber.github.io/posts/44ded5de/
2. https://hscyber.github.io/posts/7c8b9f60/
3. https://hscyber.github.io/posts/7c8b9f60/#%E4%B8%AD%E6%96%87

