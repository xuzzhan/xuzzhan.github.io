---
title: "Wechat moments likes as social network"
# toc: trues
# toc_sticky: true
---

该想法来自于在小红书上刷到一个博主ciaochaos的博客，用个人微信朋友圈的点赞数据进行网络建构和社区发现。我觉得很有意思，毕竟从来没有正视过自己的社交圈子。

# Crawling Wechat moments likes data

抓取点赞数据的代码来自开源仓库https://github.com/HYLZ-2019/FriendsOfFriends，主要用到的语言是Python。

# Visualization

<center class="half">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/post_figs/wechat-likes/fig1.jpg" alt="">
  <figcaption>Nodes are colored by betweenness centrality.</figcaption>
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/post_figs/wechat-likes/fig2.jpg" alt="">
  <figcaption>Community detection visualization.</figcaption>
</center> 
