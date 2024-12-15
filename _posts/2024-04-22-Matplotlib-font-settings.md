绘图显示中文：

```python
plt.rcParams["font.family"] = "serif"
plt.rcParams["font.serif"] = ["Microsoft YaHei"]
plt.rcParams["axes.unicode_minus"] = False #该语句解决图像中的“-”负号的乱码问题
```



参考：

https://hscyber.github.io/posts/44ded5de/

https://hscyber.github.io/posts/7c8b9f60/#%E4%B8%AD%E6%96%87