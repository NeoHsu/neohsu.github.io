---
title: "Tag cloud for Octopress"
date: 2012-06-27 16:10
categories:
- Octopress
---

Octopress的預設畫面沒有將分類標籤獨立出來，讓搜尋文章類別很不方便，因此標籤雲這東西就相對的重要，網路上找了一下相關的plugin，以下說明一下安裝與設定過程。

# Installation steps
1. Download plugin
2. Move file
3. Edit config
4. Compile octopress
<p></p>

# Download plugin
1. 下載Plugin相關檔案
[GitHub - tokkonopapa/octopress-tagcloud](https://github.com/tokkonopapa/octopress-tagcloud)
2. Plugin相關檔案
<pre class="prettyprint text">
.
├─ plugins/
│  └── tag_cloud.rb  Plugin主程式(抓取文章中的類別並丟入設定的模板中)
└─ source/
   └─ _includes/
      └─ custom/
         └─ asides/
            ├─ category_list.html 分類標籤列表模板
            └─ tag_cloud.html     分類標籤雲模板
</pre>

# Move file
將檔案移動到Octopress對應的位置
<p></p>

# Edit config
1. 編輯_config.yml<br/>
 default_asides 是設定Blog側邊欄所要放置的模板，我選擇了tag_cloud.html去作類別的顯示；  
使用的模板位置 <span style="color:#F62217">custom/asides/tag_cloud.html</span>
<pre class="prettyprint yml">
# To add custom asides, create files in /source/_includes/custom/asides/
and add them to the list like 'custom/asides/custom_aside_name.html'
default_asides:
[custom/asides/tag_cloud.html,
asides/recent_posts.html, asides/github.html, asides/twitter.html,
asides/delicious.html, asides/pinboard.html, asides/googleplus.html]
</pre>
<p></p>
# Compile octopress
<pre class="prettyprint text">
$ rake generate
$ rake preview
</pre>
