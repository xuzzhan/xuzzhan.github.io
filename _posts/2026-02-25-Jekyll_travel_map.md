---
title: "Building a user-interactive travel map"
# toc: true
# toc_label: "Outline"
# toc_sticky: true
---

我希望在个人博客中加入一个可交互的城市地图，要求：

-   托管在 GitHub Pages
-   使用 Jekyll 构建
-   城市级别记录
-   不依赖在线地图 API
-   不加载在线瓦片底图
-   纯前端实现

实现思路：使用 Leaflet + 本地 GeoJSON 数据文件。

## 一、项目结构

推荐目录结构如下：

{% include code-header.html %}

    _config.yml
    _layouts/
    assets/
        data/
            cities.geojson

## 二、创建地图页面
在 about.md 中对应位置插入：

{% include code-header.html %}

``` html
<div id="map" style="height:500px; margin-top:20px;"></div>
```

在文件最后插入：

{% include code-header.html %}

``` html
<script>
document.addEventListener("DOMContentLoaded", function() {

    var map = L.map('map');

    L.tileLayer('https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png', {
        attribution: '&copy; OpenStreetMap contributors &copy; CARTO'
    }).addTo(map);

    fetch("/assets/data/cities.geojson")
        .then(response => response.json())
        .then(data => {

          // 添加点
          var geoLayer = L.geoJSON(data, {
            onEachFeature: function (feature, layer) {
              if (feature.properties && feature.properties.name) {
                layer.bindPopup("<strong>" + feature.properties.name + "</strong>");
              }
            }
          }).addTo(map);

          // 建立 城市 → 坐标映射
          let pointMap = {};
          data.features.forEach(f => {
            if (f.geometry.type === "Point") {
              pointMap[f.properties.name] = [
                f.geometry.coordinates[1],
                f.geometry.coordinates[0]
              ];
            }
          });

          // 手动指定要连接的城市
          let connections = [
            ["xx", "xx"],
          ];

          // 画线
          connections.forEach(pair => {
            let start = pointMap[pair[0]];
            let end = pointMap[pair[1]];

            if (start && end) {
              L.polyline([start, end], {
                color: "#680b7b",
                weight: 1,
                opacity: 0.5
              }).addTo(map);
            }
          });

          map.fitBounds(geoLayer.getBounds());

        });

});
</script>
```

## 三、创建城市数据文件

路径：

{% include code-header.html %}

    assets/data/cities.geojson

示例内容：

{% include code-header.html %}

``` json
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": { "name": "Chongqing"},
      "geometry": {
        "type": "Point",
        "coordinates": [106.578, 29.571]
      }
    },
    {
      "type": "Feature",
      "properties": { "name": "Shanghai"},
      "geometry": {
        "type": "Point",
        "coordinates": [121.50, 31.246]
      }
    }
  ]
}
```

注意：GeoJSON 坐标顺序必须为 [经度, 纬度]。