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

In my previous work, my research primarily focused on *Network Resilience* and *Spatio-Temporal Modelling*, integrating methods from machine learning and statistics. As a researcher with a background in urban planning, I am dedicated to uncovering the underlying patterns within spatio-temporal big data and applying them to inform urban development policies. 

If you are interested in my experience, feel free to contact me via email at [xuzzhan@tongji.edu.cn](mailto:xuzzhan@tongji.edu.cn).


<div style="border: 1px solid var(--border-color); border-radius: 8px; padding: 10px; background-color: var(--bg-color); margin-bottom: 2em;">
    <div id="heatmap-container" style="width: 100%;"></div>
</div>


## My places
<div id="map" style="height:500px; margin-top:20px;"></div>


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
    var chartDom = document.getElementById('heatmap-container');
    
    var containerWidth = chartDom.clientWidth;
    var squareSize = (containerWidth - 40) / 53; 
    if (squareSize < 10) squareSize = 10;
    var perfectHeight = 30 + (7 * squareSize) + 35;
    chartDom.style.height = perfectHeight + 'px';

    var myChart = echarts.init(chartDom);
    var currentYear = new Date().getFullYear().toString();

    // 自动获取当前环境的文字和底色
    var style = getComputedStyle(document.documentElement);
    var textColor = style.getPropertyValue('--text-color').trim();
    var emptyColor = style.getPropertyValue('--heatmap-empty').trim();
    var BgColor = style.getPropertyValue('--bg-color').trim();

    fetch('/api/posts.json')
        .then(response => response.json())
        .then(data => {
            var wordCountByDate = {};
            var maxWords = 0;
            var totalWords = 0; 
            var totalDays = 0;

            // 统计每天字数，并找出最大值
            data.forEach(function(post) {
                if (wordCountByDate[post.date]) {
                    wordCountByDate[post.date] += post.words;
                } else {
                    wordCountByDate[post.date] = post.words;
                }
                if (wordCountByDate[post.date] > maxWords) {
                    maxWords = wordCountByDate[post.date];
                }
            });

            var chartData = [];
            var startDate = new Date(currentYear + '-01-01');
            var endDate = new Date(currentYear + '-12-31');

            // 循环遍历一年中的每一天
            for (var d = new Date(startDate); d <= endDate; d.setDate(d.getDate() + 1)) {
                // 格式化日期为 YYYY-MM-DD
                var y = d.getFullYear();
                var m = ('0' + (d.getMonth() + 1)).slice(-2);
                var day = ('0' + d.getDate()).slice(-2);
                var dateStr = y + '-' + m + '-' + day;
                var dailyWords = wordCountByDate[dateStr] || 0;

                totalWords += dailyWords;
                totalDays++;
                chartData.push([dateStr, dailyWords]);
            }

            // 动态计算分段区间, 向上取整到百位, 默认最大值 500
            var roundedMax = Math.ceil(maxWords / 100) * 100; 
            if (roundedMax === 0) roundedMax = 500; 
            var step = roundedMax / 4;

            var dynamicPieces = [
                { value: 0, color: emptyColor }, // value为0的日子，显示经典的 GitHub 浅灰色
                { gt: 0, lte: step, color: '#d8b4fe' },
                { gt: step, lte: step * 2, color: '#a855f7' },
                { gt: step * 2, lte: step * 3, color: '#7e22ce' },
                { gt: step * 3, color: '#4c1d95' }
            ];

            // ECharts 配置项
            var option = {
              title: {
                    text: 'Total words in the last ' + totalDays + ' days: ' + totalWords.toLocaleString(),
                    bottom: 0,
                    left: 0, // 紧贴画布最左侧
                    textStyle: {
                        color: textColor, // 使用类似 GitHub 的次级浅灰色，不抢热力图的视觉焦点
                        fontSize: 12,
                        fontWeight: 'normal'
                    }
                },
                tooltip: {
                    position: 'top',
                    padding: [4, 6],
                    textStyle: {
                      fontSize: 12,
                    },
                    formatter: function (p) {
                        var format = echarts.format.formatTime('yyyy-MM-dd', p.data[0]);
                        return format + ': ' + p.data[1] + ' words';
                    }
                },
                visualMap: {
                    type: 'piecewise',
                    orient: 'horizontal',
                    left: 'right',
                    bottom: 0,
                    calculable: false,
                    text: ['More', 'Less'],
                    itemSymbol: 'roundRect',
                    itemWidth: 13,
                    itemHeight: 13,
                    itemGap: 4,
                    pieces: dynamicPieces,
                    hoverLink: false
                },
                calendar: [{
                    top: 30,
                    left: 'center',
                    cellSize: [squareSize, squareSize], 
                    range: currentYear,
                    yearLabel: { show: false },
                    dayLabel: {
                        firstDay: 1, 
                        nameMap: 'EN',
                        color: textColor,
                    },
                    monthLabel: {
                        nameMap: 'EN',
                        color: textColor,
                    },
                    splitLine: {
                        show: false 
                    },
                    itemStyle: { 
                        color: 'transparent',
                        borderWidth: 1,   
                        borderColor: BgColor,
                    }
                }],
                series: [{
                    type: 'heatmap',
                    coordinateSystem: 'calendar',
                    data: chartData,
                    itemStyle: {
                      borderWidth: 3,
                      borderColor: BgColor,
                      borderRadius: 3
                    }
                }]
            };

            myChart.setOption(option);
            window.addEventListener('resize', function() {
                myChart.resize();
            });
        })
        .catch(error => console.error('Error fetching heatmap data:', error));
});
</script>


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