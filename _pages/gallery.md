---
layout: single
title: "Gallery"
permalink: /gallery/
---

<style>
.lb-close:focus,
.lb-prev:focus,
.lb-next:focus {
  outline: none !important;
  box-shadow: none !important;
}
  @media screen and (max-width: 768px) {
  .gallery-item {
    flex: 1 1 100% !important;
    max-width: 100% !important;
  }
}

@media screen and (max-width: 1024px) and (min-width: 769px) {
  .gallery-item {
    flex: 1 1 calc(50% - 20px) !important;
    max-width: calc(50% - 20px) !important;
  }
}
  <style>
  
<div class="gallery-item" style="flex: 1 1 calc(33.333% - 20px); max-width: calc(33.333% - 20px); text-align: center;">
  <a href="/assets/images/gallery_figs/{{ image.filename }}" data-lightbox="gallery" data-title="{{ image.title }}">
    <img src="/assets/images/gallery_figs/{{ image.filename }}" 
         alt="{{ image.title }}" 
         style="width: 100%; height: 200px; object-fit: cover; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.2);" />
  </a>
  <div style="margin-top: 8px; font-size: 14px; color: #555;">{{ image.title }}</div>
  {% if image.description %}
  <div style="margin-top: 4px; font-size: 13px; color: #888; line-height: 1.4;">
    {{ image.description }}
  </div>
  {% endif %}
</div>





