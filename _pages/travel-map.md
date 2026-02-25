---
title: Travel Map
layout: single
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

  // 关键：Jekyll路径写法
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