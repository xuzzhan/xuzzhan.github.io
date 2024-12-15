---
title: "Using geopandas for geocoding"
# toc: true
# toc_sticky: true
---

geopandas可以使用geopandas.tools.geocode进行对地址的地理编码，但使用该函数的时候后台不能挂着魔法梯，否则会报错。官方介绍是使用“photon”的地理编码服务，参考部分中给出了该函数的帮助文档地址。

代码如下：

```python
import geopandas as gpd
df = gpd.tools.geocode(["Tongji University, Shanghai, China"])
df
```

```ruby
module Jekyll
  class TagIndex < Page
    def initialize(site, base, dir, tag)
      @site = site
      @base = base
      @dir = dir
      @name = 'index.html'
      self.process(@name)
      self.read_yaml(File.join(base, '_layouts'), 'tag_index.html')
      self.data['tag'] = tag
      tag_title_prefix = site.config['tag_title_prefix'] || 'Tagged: '
      tag_title_suffix = site.config['tag_title_suffix'] || '&#8211;'
      self.data['title'] = "#{tag_title_prefix}#{tag}"
      self.data['description'] = "An archive of posts tagged #{tag}."
    end
  end
end
```

得到结果：

|      | geometry                   | address                                                |
| :--- | :------------------------- | :----------------------------------------------------- |
| 0    | POINT (121.50209 31.28441) | 同济大学, 彰武路, 200092, 彰武路, 杨浦区, 上海市, 中国 |



参考：

官方链接：https://geopandas.org/en/stable/docs/reference/api/geopandas.tools.geocode.html

