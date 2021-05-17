---
title: "Setup Octopress under Mac OS X"
date: 2013-11-28 21:30:08 +0800
categories:
- Octopress
- Homebrew
---

# Outline
+ 在 Mac 上建置 Octopress 環境撰寫 Blog
+ 安裝 Mac 的第三方套件庫 ( Homebrew ) 建置 Ruby 環境
<p></p>

# Install Homebrew & Ruby
## Install Homebrew
Homebrew 是一套在 Mac OS X 下使用的套件管理工具，以往大家會使用 Mac Ports ，但是 Port 的套件相依性太深，常常會為了裝個小套件而跟著裝上一堆用不到的相依套件； Homebrew 則盡量依賴系統內已經有的套件、它的套件 formula 是用簡單的 Ruby 寫的，所以你可以 fork Homebrew repository 自行修改維護、它自己用 Git 管理自己，升級十分方便；Homebrew 安裝好套件後不會幫你做 symlink 到 /usr/local ，避免你的原生系統內還有相同的套件因而不相容而打架
<pre class="prettyprint text">
$ ruby -e "$(curl -fsSL https://raw.github.com/mxcl/homebrew/go/install)"
</pre>

## Use Homebrew Install rbenv & ruby-build
rbenv 是用來管理 Ruby 版本控制的工具，ruby-build 則是用來編譯安裝 Ruby
<pre class="prettyprint text">
$ brew install rbenv ruby-build
</pre>

## Add Environment Variables
設定 rbenv 的路徑並確保有 rbenv 工具；依照你使用的環境去設定各自的環境變數 ( zprofile / bash_profile )
<pre class="prettyprint text">
$ echo 'export RBENV_ROOT=/usr/local/var/rbenv' >> ~/.zprofile
$ echo 'if which rbenv > /dev/null; then eval "$(rbenv init -)"; fi' >> ~/.zprofile
</pre>
> 修改完環境變數的檔案要重新讀入環境設定檔 source ~/.zprofile

## Use rbenv Install Ruby & Check Ruby Versions
利用 rbenv 安裝各版本的 Ruby ，* 顯示目前在此目錄下使用的 Ruby 版本
<pre class="prettyprint text">
$ rbenv install 1.9.3-p448
$ rbenv versions
* system (set by /usr/local/var/rbenv/version)
  1.9.3-p448
</pre>

## Setting Ruby Version to Build Octopress
進入從 github 上 clone 下來的 Octopress 目錄中，設定要編譯 Octopress 的 Ruby 版本
<pre class="prettyprint text">
$ cd ~/Octopress
$ echo '1.9.3-p448' > .ruby-version
</pre>

## Restart Terminal & Check Ruby Version
重新啟動 Terminal 並進入 Octopress 目錄下檢查當前目錄使用的 Ruby 版本
<pre class="prettyprint text">
$ rbenv versions
  system
* 1.9.3-p448 (set by /Users/neohsu/Blog/.ruby-version)
</pre>

#Setup & Use Octopress
## Setup Octopress
<pre class="prettyprint text">
$ gem install bundler
# If you use rbenv, rehash to be able to run the bundle command
$ rbenv rehash
$ bundle install
# 如果是第一次安装 Octopress ，需要先安装預設的 theme ( rake install )
$ rake install
</pre>

## Use Octopress  
<p></p>
1. 建立文章
2. 編譯 Markdown 文章
3. 預覽
<pre class="prettyprint text">
$ rake new_post["Zombie Ninjas Attack: A survivor's retrospective"]
# Creates source/_posts/2011-07-03-zombie-ninjas-attack-a-survivors-retrospective.markdown
$ rake generate   # Generates posts and pages into the public directory
$ rake preview    # Watches, and mounts a webserver at http://localhost:4000
</pre>

如果你的 Shell 是用 zsh 的話，上面這個指令可能會讓你出現這樣的狀況：  

> zsh : no matches found : new_post[how-to-instala-octopresa]  

如果遇到這個狀況的話，你可改一下你的 .zshrc ，加上這行：  

> alias rake="noglob rake"

或是改一下原來的 rake 指令：  

> rake "new_post[how-to-install-octopress]"
