---
title: "How to Install Octopress on Ubuntu"
date: 2011-11-24 01:54
categories:
- Ubuntu
- Octopress
---

# Development environment
* Ubuntu 11.10 64 bit
* Dell Studio XPS 1640  

# Installation steps  
1. Basic components
2. Git
3. RVM + Bundler
4. Octopress  

# Install basic components

```
$ sudo aptitude install bash curl build-essential bison openssl <br>libreadline6 libreadline6-dev zlib1g zlib1g-dev libssl-dev <br>libyaml-dev libsqlite3-0 libsqlite3-dev sqlite3 libxml2-dev <br>libxslt-dev autoconf libc6-dev ncurses-dev automake
```

# Install Git
安裝Git並下載最新版本來重新編譯  
build-dep : 會替你安裝build 所需要的所有package

```
$ sudo aptitude build-dep git-core
$ wget http://git-core.googlecode.com/files/git-1.7.7.2.tar.gz
$ tar xvzf git-1.7.7.2.tar.gz
$ cd git-1.7.7.2/
$ ./configure
$ make
$ sudo make install
```

# Install RVM & Bundler
Bundler : 用來管理Ruby程式
```
$ bash < <(curl -s https://rvm.beginrescueend.com/install/rvm)
$ echo '[[ -s "$HOME/.rvm/scripts/rvm" ]] && . "$HOME/.rvm/scripts/rvm" # Load RVM function' >> ~/.bash_profile
$ source .bash_profile
$ rvm pkg install openssl
$ rvm pkg install iconv
$ rvm install 1.9.2 -C --with-openssl-dir=$HOME/.rvm/usr,--with-iconv-dir=$HOME/.rvm/usr
$ rvm use 1.9.2 --default
$ rvm reload
$ rvm rubygems latest
$ gem install bundler
```

# Install Octopress
```
$ git clone git://github.com/imathis/octopress.git octopress
$ cd octopress
$ bundle install
$ rake install
```
