---
layout: single
permalink: /
excerpt: "One apple a day, keep the doctor away."
title: About Me
header:
  overlay_image: /assets/images/home_header.jpg
  overlay_filter: linear-gradient(rgba(255, 20, 147, 0.5), rgba(0, 0, 128, 0.5))
sidebar: true
# toc: true
# toc_label: "Outline"
# toc_sticky: true
---

Hi, I am xuzzhan.  I am an PhD student at Tongji University, under the guidance of Assoc. Prof. Shen Yao (Director of [TJ-CUSP](https://www.planningscience.cn/ )). This website is primarily used to share my research work and study notes. 

In my previous work, my research primarily focused on urban network resilience and spatio-temporal modeling, integrating methods from machine learning and statistics. As a researcher with a background in urban planning, I am dedicated to uncovering the underlying patterns within spatio-temporal big data and applying them to inform urban development policies. 

If you are interested in my experience, feel free to contact me via email at [xuzzhan@tongji.edu.cn](mailto:xuzzhan@tongji.edu.cn).

## My places
<div id="map" style="height:500px; margin-top:20px;"></div>


## Research interests
- Urban complexity and modelling
- Network resilience and optimization
- Urban mobility and accessibility

## Education
- *2022 – 2025*, *M.Phil.* in Urban and Rural Planning, Tongji University, Shanghai, China.
- *2017 – 2022*, *B.Sc.* in Urban and Rural Planning, Chongqing University, Chongqing, China.

## Publications
**Journal Articles**
- Feng, Y., **Xu, Z.**, Qi, J., & Shen, Y.* (2025). *Spatial Reconfiguration of Housing Price Patterns and Submarkets in Shanghai Before and After COVID-19.* Land, 14(10), 2008.  
- Shen, Y.\*, Liu, L., **Xu, Z.**, Yan, W., & Xu, L. (2024). *Varying Road Network Resilience of Chinese Cities.* Environment and Planning B: Urban Analytics and City Science, 51(5), 1168–1173.  
- Shen, Y.\*, **Xu, Z.**, & Feng, Y. (2025). *Research on the Measurement and Optimization of the Resilience of Commuting Spatio-temporal Structure in Shanghai during Multi-level Flood Disasters.* Shanghai Urban Planning Review, (02), 40–50. (In Chinese)  
- **Xu, Z.**, Chen, J., Wu, X., Feng, Y., Du, Y., & Shen, Y.\* (2025). *Study on Intelligent Enhancement of Multi-dimensional Fire Resilience in Historic Heritage Spaces.* Journal of Urban and Regional Planning, 17(1), 1–18. (In Chinese)  
- Feng, Y., Xu, Y., **Xu, Z.**, Liu, L., & Shen, Y.\* (2025). *Study on Automated Identification Methods in Urban Texture Based on Complex Networks: A Case Study of the Middle Ring Area of Shanghai.* Shanghai Urban Planning Review. (Accepted; In Chinese)  
- **Xu, Z.**, Feng, Y., Song, Q., & Shen, Y.\* (under review). *Fractal-Informed Natural Submarkets: A Bottom-Up Approach to Segment Housing Markets in the Yangtze River Delta.*  

**Conferences**
- **Xu, Z.**, & Shen, Y.\* (2024). *Measuring Spatial Network Resilience of Shanghai Using Supply-Demand Flow.* The 64th ACSP Annual Conference.  
- **Xu, Z.**, Feng, Y., Wu, X., & Shen, Y.\* (2024). *Natural Housing Markets as Spatial Fractals.* The 1st AsiaCarto 2024 Conference.  


<script>
document.addEventListener("DOMContentLoaded", function() {

    var map = L.map('map');

    L.tileLayer('https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png', {
        attribution: '&copy; OpenStreetMap contributors &copy; CARTO'
    }).addTo(map);

    // fetch("/assets/data/cities.geojson")
    //     .then(response => response.json())
    //     .then(data => {

    //         var geoLayer = L.geoJSON(data, {
    //             onEachFeature: function (feature, layer) {
    //                 if (feature.properties && feature.properties.name) {
    //                     layer.bindPopup(
    //                         "<strong>" + feature.properties.name + "</strong>"
    //                     );
    //                 }
    //             }
    //         }).addTo(map);

    //         map.fitBounds(geoLayer.getBounds());

    //     });

    fetch("/assets/data/cities.geojson")
        .then(response => response.json())
        .then(data => {

          // 添加点
          // var geoLayer = L.geoJSON(data, {
          //   onEachFeature: function (feature, layer) {
          //     if (feature.properties && feature.properties.name) {
          //       layer.bindPopup("<strong>" + feature.properties.name + "</strong>");
          //     }
          //   }
          // }).addTo(map);

          // 1. 先定义你自己的图标
          var customIcon = L.icon({
            iconUrl: '/assets/images/location.svg', 
            iconSize: [40, 40],              // 图标尺寸 [宽, 高]
            iconAnchor: [20, 35],            // 图标锚点：让图标尖端对准坐标点
            popupAnchor: [0, -40]            // 弹窗的锚点，相对于图标位置
          });

          // 2. 加载GeoJSON时，使用自定义图标渲染点
          var geoLayer = L.geoJSON(data, {
            pointToLayer: function (feature, latlng) {
              // 使用自定义图标创建标记点
              return L.marker(latlng, { icon: customIcon });
            },
            // 保留你原有的弹窗逻辑
            onEachFeature: function (feature, layer) {
              if (feature.properties && feature.properties.name) {
                layer.bindPopup("<strong>" + feature.properties.name + "</strong>");
              }
            }
          }).addTo(map);

          // 建立 id → 坐标映射
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
            ["Chongqing", "Hongkong"],
            ["Chongqing", "Shanghai"],
            ["Shanghai", "Hongkong"],
            ["Chongqing", "Shenzhen"],
            ["Hongkong", "Shenzhen"],
            ["Shanghai", "Guangzhou"],
            ["Guangzhou", "Shenzhen"],
            ["Chongqing", "Dali"],
            ["Chongqing", "Tailand"],
            ["Chongqing", "Suzhou"],
            ["Suzhou", "Nanjing"],
            ["Nanjing", "Chongqing"],
            ["Shanghai", "Hangzhou"],
            ["Shanghai", "Wuxi"],
            ["Wuxi", "Chongqing"],
            ["Chongqing", "Guilin"],
            ["Chongqing", "Hainan"],
            ["Chongqing", "Jiuzhaigou"],
            ["Dali", "Luguhu"],
            ["Luguhu", "Daocheng"],
            ["Nanjing", "Ma'anshan"],
            ["Chongqing", "Chengdu"],
          ];

          // 画线
          connections.forEach(pair => {
            let start = pointMap[pair[0]];
            let end = pointMap[pair[1]];

            if (start && end) {
              L.polyline([start, end], {
                color: "#680b7b",
                weight: 2,
                opacity: 0.5
              }).addTo(map);
            }
          });

          map.fitBounds(geoLayer.getBounds());

        });

});
</script>