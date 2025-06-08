---
layout: single
title: "Gallery"
permalink: /gallery/
---

<link href="https://cdn.jsdelivr.net/npm/lightbox2@2/dist/css/lightbox.min.css" rel="stylesheet" />

<script src="https://cdn.jsdelivr.net/npm/lightbox2@2/dist/js/lightbox-plus-jquery.min.js"></script>

<style>
.lb-close:focus,
.lb-prev:focus,
.lb-next:focus {
  outline: none !important;
  box-shadow: none !important;
}
</style>

<div style="display: flex; flex-wrap: wrap; gap: 20px; justify-content: center; padding: 10px;">
  {% for image in site.data.gallery %}
  <div style="width: 300px; text-align: center;">
    <a href="/assets/images/gallery_figs/{{ image.filename }}" data-lightbox="gallery" data-title="{{ image.title }}">
      <img src="/assets/images/gallery_figs/{{ image.filename }}" 
           alt="{{ image.title }}" 
           style="width: 100%; height: 200px; object-fit: cover; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.2);" />
    </a>
    <div style="margin-top: 8px; font-size: 14px; color: #555;">{{ image.title }}</div>
  </div>
  {% endfor %}
</div>
