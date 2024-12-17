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

<table style="border-collapse:collapse;border:1px solid #ccc;margin:0 auto;margin-bottom:20px;">
    <tr>
      <th style="width: 50em;;border:1px solid #ccc;padding:8px;text-align:center;">字体名称</th>
      <th style="width: 50em;;border:1px solid #ccc;padding:8px;text-align:center;">别名</th>
    </tr>
    <tr>
      <td style="border:1px solid #ccc;padding:8px;text-align:center;">宋体</td>
      <td style="border:1px solid #ccc;padding:8px;text-align:center;">SimSun</td>
    </tr>
    <tr>
      <td style="border:1px solid #ccc;padding:8px;text-align:center;">黑体</td>
      <td style="border:1px solid #ccc;padding:8px;text-align:center;">Simhei</td>
    </tr>
    <tr>
      <td style="border:1px solid #ccc;padding:8px;text-align:center;">楷体</td>
      <td style="border:1px solid #ccc;padding:8px;text-align:center;">KaiTi</td>
    </tr>
    <tr>
      <td style="border:1px solid #ccc;padding:8px;text-align:center;">等线</td>
      <td style="border:1px solid #ccc;padding:8px;text-align:center;">DengXian</td>
    </tr>
    <tr>
      <td style="border:1px solid #ccc;padding:8px;text-align:center;">仿宋</td>
      <td style="border:1px solid #ccc;padding:8px;text-align:center;">FangSong</td>
    </tr>
    <tr>
      <td style="border:1px solid #ccc;padding:8px;text-align:center;">微软雅黑</td>
      <td style="border:1px solid #ccc;padding:8px;text-align:center;">Microsoft YaHei</td>
    </tr>
    <tr>
      <td style="border:1px solid #ccc;padding:8px;text-align:center;">华文宋体</td>
      <td style="border:1px solid #ccc;padding:8px;text-align:center;">STSong</td>
    </tr>
    <tr>
      <td style="border:1px solid #ccc;padding:8px;text-align:center;">华文中宋</td>
      <td style="border:1px solid #ccc;padding:8px;text-align:center;">STZhongsong</td>
    </tr>
    <tr>
      <td style="border:1px solid #ccc;padding:8px;text-align:center;">华文楷体</td>
      <td style="border:1px solid #ccc;padding:8px;text-align:center;">STKaiti</td>
    </tr>
    <tr>
      <td style="border:1px solid #ccc;padding:8px;text-align:center;">方正舒体</td>
      <td style="border:1px solid #ccc;padding:8px;text-align:center;">FZShuTi</td>
    </tr>
    <tr>
      <td style="border:1px solid #ccc;padding:8px;text-align:center;">华文新魏</td>
      <td style="border:1px solid #ccc;padding:8px;text-align:center;">STXinwei</td>
    </tr>
    <tr>
      <td style="border:1px solid #ccc;padding:8px;text-align:center;">方正姚体</td>
      <td style="border:1px solid #ccc;padding:8px;text-align:center;">FZYaoTi</td>
    </tr>
  </table>


# 中英字体混合显示

另一个问题是可能需要同时显示中文和英文字体，这个时候要用到matplotlib在2022年9月更新的新功能，具体可以查阅官方文档：https://matplotlib.org/stable/users/prev_whats_new/whats_new_3.6.0.html#fonts-and-text。

简单而言，就是设置两类字体（前英文，后中文）。

```python
plt.rcParams["font.family"] = ["Times New Roman", "SimSun"]
```

例如，在这里我设置英文用Times New Roman，中文用等线Dengxian。

<figure style="width: 35%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/post_figs/matplotlib-font-settings/test.jpg" alt="">
  <!-- <figcaption>Itty-bitty caption.</figcaption> -->
</figure> 

参考：

1. https://hscyber.github.io/posts/44ded5de/
2. https://hscyber.github.io/posts/7c8b9f60/
3. https://hscyber.github.io/posts/7c8b9f60/#%E4%B8%AD%E6%96%87

