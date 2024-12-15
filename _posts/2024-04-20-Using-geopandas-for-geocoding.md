geopandas可以使用geopandas.tools.geocode进行对地址的地理编码，但使用该函数的时候后台不能挂着魔法梯，否则会报错。官方介绍是使用“photon”的地理编码服务，参考部分中给出了该函数的帮助文档地址。

代码如下：

```python
import geopandas as gpd
df = gpd.tools.geocode(["Tongji University, Shanghai, China"])
df
```

得到结果：

|      | geometry                   | address                                                |
| :--- | :------------------------- | :----------------------------------------------------- |
| 0    | POINT (121.50209 31.28441) | 同济大学, 彰武路, 200092, 彰武路, 杨浦区, 上海市, 中国 |



**参考：**

官方链接：https://geopandas.org/en/stable/docs/reference/api/geopandas.tools.geocode.html

