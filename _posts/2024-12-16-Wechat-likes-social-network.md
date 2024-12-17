---
title: "Wechat moments likes as social network"
# toc: trues
# toc_sticky: true
---

该想法来自于在小红书上刷到一个博主ciaochaos的博客，用个人微信朋友圈的点赞数据进行网络建构和社区发现。我觉得很有意思，毕竟从来没有正视过自己的社交圈子。

# Crawling Wechat moments likes data

抓取点赞数据的代码来自开源仓库https://github.com/HYLZ-2019/FriendsOfFriends，主要用到的语言是Python。其核心的逻辑是通过爬取电脑端微信朋友圈界面的信息，存储到一个pickle二进制文件中。再重新读取该文件转为后续生成网络的所需格式。

当然，作者的实现是基于.js文件通过html进行可视化。我对这方面研究不多。想要调整一些外观参数和实现更好看的网络绘图，我把数据重新调整了一下变成Python可读取的.json文件。后面就比较基础了，使用Networkx包录入节点和边的信息，就可以进行可视化和社区发现等步骤。

# Visualization

微信朋友圈应该存在一个时限，当你往前翻到足够早就翻不动了。我实际实验下来，我能爬到最早十月中旬的数据，总共800+条好友朋友圈，涉及170+位好友。

虽然是我自己的朋友圈，但是我一直不怎么点赞，所以我和其他人的互动记录很少。因此，在网络中我的节点度相当小。但是，其他人互动的频率也不高。好像现在大部分人都慢慢没有点赞的习惯了。网络中甚至有大量的孤立节点存在，或者形成孤立的几个零星节点对。

简单跑了一下社区发现，基本把我不同阶段的社交圈子能划分出来。跟我现在联系最紧密的几个社区，黄色、橙色和绿色都是我的本科生与研究生同学。其他一些具有一定规模的社团要么是我的高中同学，要么是小学同学，或者是一些研究生同学自身的社交圈，还包含部分老师。

总体而言，我的社交网络并不大，总共也就170+个节点和不到100条边。但看看自己的社交网络特征，还是挺有意思的一件事。

<figure class="half">
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/post_figs/wechat-likes/fig1.jpg" width="45%">
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/post_figs/wechat-likes/fig2.jpg" width="45%">
    <figcaption>Visualization of the likes social network and community detection in WeChat Moments. The color of the node on the left indicates the betweenness centrality, and the same color on the right indicates the same community.</figcaption>
</figure>




