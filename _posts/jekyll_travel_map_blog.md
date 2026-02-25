---
categories:
- Tech
- Blog
date: 2026-02-25
layout: post
tags:
- Jekyll
- GitHub Pages
- Leaflet
- GeoJSON
- Visualization
title: 在 GitHub Pages（Jekyll）上构建可交互的旅行城市地图（无在线底图）
---

## 目标

在个人博客中加入一个可交互的旅行地图，要求：

-   托管在 GitHub Pages
-   使用 Jekyll 构建
-   城市级别记录
-   不依赖在线地图 API
-   不加载在线瓦片底图
-   纯前端实现

实现思路：使用 Leaflet + 本地 GeoJSON 数据文件。

------------------------------------------------------------------------

## 一、项目结构

推荐目录结构如下：

    _config.yml
    _layouts/
    assets/
        data/
            cities.geojson
    pages/
        travel-map.md

------------------------------------------------------------------------

## 二、创建地图页面

新建文件：

    pages/travel-map.md

内容如下：

``` markdown
---
layout: default
title: Travel Map
permalink: /travel-map/
---

<h2>Visited Cities</h2>

<div id="map" style="height:600px;"></div>

<link rel="stylesheet"
 href="https://unpkg.com/leaflet/dist/leaflet.css"/>

<script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>

<script>
document.addEventListener("DOMContentLoaded", function() {

  var map = L.map('map', {
    zoomControl: true,
    attributionControl: false
  }).setView([30, 100], 3);

  fetch('{{ "/assets/data/cities.geojson" | relative_url }}')
  .then(res => res.json())
  .then(data => {

    L.geoJSON(data, {
      pointToLayer: function(feature, latlng) {
        return L.circleMarker(latlng, {
          radius: 6,
          fillOpacity: 0.8
        });
      },
      onEachFeature: function(feature, layer) {
        layer.bindPopup(
          "<b>" + feature.properties.city + "</b><br>" +
          feature.properties.country + "<br>" +
          feature.properties.note
        );
      }
    }).addTo(map);

  });

});
</script>
```

------------------------------------------------------------------------

## 三、创建城市数据文件

路径：

    assets/data/cities.geojson

示例内容：

``` json
{
"type": "FeatureCollection",
"features": [
{
"type": "Feature",
"properties": {
"city": "Tokyo",
"country": "Japan",
"note": "2023 留学"
},
"geometry": {
"type": "Point",
"coordinates": [139.6917, 35.6895]
}
},
{
"type": "Feature",
"properties": {
"city": "Paris",
"country": "France",
"note": "2024 旅行"
},
"geometry": {
"type": "Point",
"coordinates": [2.3522, 48.8566]
}
}
]
}
```

注意：GeoJSON 坐标顺序必须为 `[经度, 纬度]`。

------------------------------------------------------------------------

## 四、本地预览方法

在博客根目录运行：

    bundle install
    bundle exec jekyll serve

浏览器访问：

    http://127.0.0.1:4000/

如需自动刷新：

    bundle exec jekyll serve --livereload

------------------------------------------------------------------------

## 总结

该方案完全静态，不依赖在线地图服务，数据可控，与 Jekyll
结构兼容，可长期稳定运行。
