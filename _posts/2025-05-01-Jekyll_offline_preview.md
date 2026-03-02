---
title: "How to preview a Jekyll site offline"
categories:
  - Website

# toc: true
# toc_label: "Outline"
# toc_sticky: true
---

在将博客部署到 GitHub Pages 之前，在本地完整预览网站效果，包括：

-   正确解析 Liquid 模板
-   正确处理 baseurl
-   加载静态资源（CSS / JS / GeoJSON）
-   支持自动刷新

## 一、确认项目是标准 Jekyll 结构

在博客根目录应包含：
{% include code-header.html %}

```html
    _config.yml
    Gemfile
    _layouts/
    _posts/
    assets/
```

如果没有 `Gemfile`，建议补充一个。

## 二、环境准备

### 1. 检查 Ruby 是否安装

终端输入：
{% include code-header.html %}
    ruby -v

如果未安装，需要先安装 Ruby。再输入 `gem install bundler jekyll` 安装 Bundler 与 Jekyll, 若已经安装则跳过。

## 三、安装项目依赖

进入博客根目录：
{% include code-header.html %}
    cd 博客目录

然后运行 `bundle install` 根据 Gemfile 安装依赖，若已经安装则跳过。

## 四、本地启动服务器

运行：
{% include code-header.html %}
    bundle exec jekyll
    # 如果需要在本地测试评论系统（如 giscus），必须以 production 环境启动
    JEKYLL_ENV=production bundle exec jekyll serve

成功后终端会显示：
{% include code-header.html %}
    Server address: http://127.0.0.1:4000/

浏览器打开：
{% include code-header.html %}
    http://127.0.0.1:4000/

即可查看本地版本。

## 五、开启自动刷新（推荐）
{% include code-header.html %}
    bundle exec jekyll serve --livereload

修改文件后浏览器会自动刷新。

## 总结

安装后标准流程是：

1.  ruby -v
2.  cd 博客目录
3.  bundle exec jekyll serve
4.  http://127.0.0.1:4000/
