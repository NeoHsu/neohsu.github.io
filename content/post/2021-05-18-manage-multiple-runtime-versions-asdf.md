---
title: "Manage multiple runtime versions - asdf"
date: 2021-05-18T14:58:57Z
categories:
- asdf
- NVM
- runtime
- Go
- NodeJS
---

最近在 [Node.js 台灣](https://www.facebook.com/groups/262800543746083) 看到有人在介紹如何安裝 [NodeJS](https://nodejs.org/en/)  
其實有在使用 NodeJS 當 Backend 的 developer 通常都會用 [NVM]() 來進行安裝，  
原因是可以隔離作業系統本身的檔案，畢竟 JavaScript 的撰寫都會比較飄逸，有時候會在意想不到的地方寫了一些檔案 Orz  
另外還可以自由切換 NodeJS 版本，主要也是 NodeJS 前期迭代版本比較快速需要多版本測試一下 XDD  

在有 runtime engine 的語言蠻常延伸出多版本管理的工具，例如 gvm, nvm, rbenv, pyenv...  
自己之前再撰寫 NodeJS 時一開始也是使用了 NVM 管理 NodeJS 版本，  
但之後開始撰寫 Golang 後，也套用了 gvm 來做版本控制，  
用了一段時間後覺得要進行兩套版本管理的 tool 覺得蠻厭世的，  
就去尋找新的替代方案，結果找到了 [asdf](https://asdf-vm.com/)  
> Manage multiple runtime versions with a single CLI tool

這標題下得根本就是我想找的東西啊!!!  
**asdf** 操作上蠻直覺的，下面就介紹一下它該如何操作

# Install asdf
**asdf** 文件中提供了蠻多種安裝方式，但官方說使用 HomeBrew 安裝會有點 [issue](https://github.com/asdf-vm/asdf/issues/785)  
最後我選擇用 Git 方式安裝 asdf，由於自己有在管理 [dotfiles](https://github.com/NeoHsu/dotfiles) 透過 Git Submodule 安裝還蠻順手的，

```shell
git clone https://github.com/asdf-vm/asdf.git ~/.asdf --branch v0.8.0
```

將工具設定到自己的 zshrc 中

```shell
# 設定 asdf 腳本
source $HOME/.asdf/asdf.sh
# 設定 asdf 指令補全
source $HOME/.asdf/completions/asdf.bash
```

# Install runner engine
在 **asdf** 的架構下，我們需要加 plugin 來對應到指定的 runner engine 

```shell
# asdf plugin add <name>
asdf plugin add golang
asdf plugin add nodejs
```

這時候還沒安裝 **runner engine** 到你的機器上，只是單純和 **asdf** 説我想要安裝什麼 **runner engine**，  
你可以透過下面指令找出目前官方有的 **runner engine** 版本

```shell
# asdf list all <name>
asdf list all golang
# 相當於 nvm ls-remote 的效果
asdf list all nodejs
```

選擇你要安裝的版本後，透過下面指令安裝

```shell
# asdf install <name> <version>
asdf install golang 1.16.4
asdf install nodejs 16.1.0
```

安裝完後可以檢查一下已安裝的 runner engine

```shell
# asdf list <name>
asdf list golang
asdf list nodejs
```

# Set Current Version
經過上面的操作後，我們只是安裝好想要的 runner engine 還沒設定當下要使用的版本，  
在 **asdf** 提供了 3 種設定使用版本的方式:
1. **global**: 會建立 $HOME/.tool-versions 檔案紀錄設定的版本
    ```shell
    # asdf global <name> <version>
    asdf global golang 1.16.4
    asdf global nodejs 16.1.0
    ```
> 類似 git global config 設定
2. **shell**: 會在目前的 shell session 紀錄設定的版本
    ```shell
    # asdf shell <name> <version>
    asdf shell golang 1.16.4
    asdf shell nodejs 16.1.0
    ```
3. **local**: 會在當前的資料夾內建立 .tool-versions 檔案紀錄設定的版本
    ```shell
    # asdf local <name> <version>
    asdf local golang 1.16.4
    asdf local nodejs 16.1.0
    ```
> 類似 git config 設定

可以透過下面指令看當前設定的版本

```shell
asdf current
```

以上簡單介紹一下 **asdf** 的操作，希望能讓剛接觸的人馬上入手使用，  
其實還有一些 **environment** 設定可以操作，  
大家熟悉後可以再進階研究一下喔！