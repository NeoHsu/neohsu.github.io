---
title: "How to fix build warning layout nil requested on Octopress"
date: 2014-11-01 18:29:26 +0800
categories:
- Octopress
---

想要將之前寫的文章重新 Build 成靜態網頁的時候發生了錯誤，主要原因應該是對於 nill 值無法判定導致錯誤的問題，只要將有出現錯誤的檔案將 nill 改成 null 即可

<pre class="prettyprint text">
$ rake generate
## Generating Site with Jekyll
unchanged sass/screen.scss
Configuration file: /Users/Neo/Octopress/_config.yml
            Source: source
       Destination: public
      Generating...
     Build Warning: Layout 'nil'
       requested in blog/categories/ubuntu/atom.xml does not exist.
     Build Warning: Layout 'nil'
       requested in blog/categories/octopress/atom.xml does not exist.
     Build Warning: Layout 'nil'
       requested in blog/categories/java/atom.xml does not exist.
     Build Warning: Layout 'nil'
        requested in blog/categories/environment/atom.xml does not exist.
     Build Warning: Layout 'nil'
        requested in blog/categories/homebrew/atom.xml does not exist.
     Build Warning: Layout 'nil'
        requested in blog/categories/android/atom.xml does not exist.
     Build Warning: Layout 'nil'
        requested in blog/categories/eclipse/atom.xml does not exist.
     Build Warning: Layout 'nil'
        requested in blog/categories/ubuntu/atom.xml does not exist.
     Build Warning: Layout 'nil'
        requested in blog/categories/octopress/atom.xml does not exist.
     Build Warning: Layout 'nil'
        requested in blog/categories/java/atom.xml does not exist.
     Build Warning: Layout 'nil'
        requested in blog/categories/environment/atom.xml does not exist.
     Build Warning: Layout 'nil'
        requested in blog/categories/homebrew/atom.xml does not exist.
     Build Warning: Layout 'nil'
        requested in blog/categories/android/atom.xml does not exist.
     Build Warning: Layout 'nil'
        requested in blog/categories/eclipse/atom.xml does not exist.
                    done.
 Auto-regeneration: disabled. Use --watch to enable.
</pre>

Fix this error by replacing from file:
> layout: nil

with:
> layout: null

in files:

+ **source/atom.xml**
+ **source/_includes/custom/category_feed.xml**
