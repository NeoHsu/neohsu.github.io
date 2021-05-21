---
title: "Manager dotfiles with dotbot"
date: 2021-05-19T14:47:44Z
categories:
- dotbot
- dotfile
- asdf
- Tmux
- Git
- Antibody
- Blackbox
- HomeBrew
---
![screenshot for osx](https://raw.githubusercontent.com/NeoHsu/dotfiles/master/screenshot/osx_dotfile.png)

最近重新整理了一下自己的 [dotfiles](https://github.com/NeoHsu/dotfiles)，自己認為 **dotfiles** 需要有幾個重點，
- **版本控制**  
每次更改 dotfile 都需要有紀錄可以回朔，有些工具一開始設定後可能只是試驗性質，還沒融入自己的開發流程中，如果不習慣可以 rollback 回原本設定
- **區隔 workspace**  
能夠在不同 workspace 上對應到不同的 dotfile 設定，像是我們常常會有一些公司使用的特別工具設定，自己有時後會不想將這些特別設定混在自己的 dotfile 中，主要還是透過 folder 結構去做整體區隔
- **轉移快速**  
最好是能夠用ㄧ個指令就設定好所有的 dotfile 設定到新的機器中
- **工具各司其職**  
拿捏什麼類的工具用什麼東西來做管理，重新定義自己對於管理工具的處理方式

以下是自己針對項目規劃自己的 [dotfiles](https://github.com/NeoHsu/dotfiles)
```zsh
.
├─ .blackbox/
├─ asdf
├─ dotbot
├─ gitrc/
│  ├─ global.gitconfig
│  ├─ personal.gitconfig
│  └─ work/
│     └─ company.gitconfig
├─ tmux/
│  └─ tmux.conf
├─ zsh/
│  ├─ zshrc
│  ├─ zsh_plugins
│  ├─ aliases/
│  │  ├─ common.zsh
│  │  └─ fzf.zsh
│  ├─ exports/
│  │  ├─ work/
│  │  │  └─ company.zsh
│  │  ├─ common.zsh
│  │  └─ personal.zsh
│  ├─ functions/
│  │  └─ brew-clean.zsh
│  └─ themes/
│     └─ p10k.zsh
├─ Brewfile
├─ git-czrc.json
├─ tool-versions
├─ install.conf.yaml
└─ install
```
這邊就一一介紹一下大致上使用的工具吧！

## dotbot
這是一套協助將 dotfile 放置到 $HOME 目錄中的工具，主要透過 **install.conf.yml** 檔案設定，
透過設定檔去配置你的 dotfile 放置到哪個位置。

在使用 [dotbot](https://github.com/anishathalye/dotbot) 之前，自己曾經是用過 [yadm](https://yadm.io/) 來管理 dotfile，
兩者在設計上有點不一樣，在 yadm 中是將你的 $HOME 目錄下當作一個 repo，當你想要更改或加入設定的時候，透過簡單的類似 Git 指令去做相關操作，
其實底層就是用 Git 去運作。  

一開始覺得很方便，不需要特別去規劃整個 folder 架構就可以直接做管理，  
不過後續需要從比較廣的角度來管理重構自己的 dotfile 的時候反而不太容易，  
譬如我想要在一個 editor 開啟所有 dotfile 設定的時候，他因為是直接把 $HOME 目錄當做整體的 repo，這時候我就會看到蠻多不屬於 dotfile 的檔案，
當時唯一解法就是 clone 下我之前設定的 dotfile 開啟來看...這有點土砲的作法。  

後續看到 [dotbot](https://github.com/anishathalye/dotbot) 這工具，
他主要是透過一個專案的 repo 去管理你的所有 dotfile，
透過設定檔去放置你的 dotfile ，主要用 link 去做處理，
瞬間在管理上就順手多了，
其實如果自己本身有要整理和管理 dotfile 檔案，其實不太需要飄逸管理的 [yadm](https://yadm.io/) ，
因為你本來就想要有組值得做這件事情了

## asdf
一個多語言的 runner engine 的管理工具，類似 gvm, nvm...的工具，
這邊關於工具操作的介紹在我之前的文章有提到過 [MANAGE MULTIPLE RUNTIME VERSIONS - ASDF](/post/2021-05-18-manage-multiple-runtime-versions-asdf)，
這邊再補充一下其實目前在 asdf encosystem 的發展下，該工具已經不在指是針對 runtime engine 的版本管理了，
目前也開始有開發者協助幫忙支援一些 CLI tools 的安裝，有點類似 MacOS 中的 HomeBrew 的感覺，
但是自己是認為還是將 asdf 當作 runner engine 管理版本的工具就好，
畢竟它和 homebrew 的運作方式不同，沒有對應到 linux 相關的系統結構，
因此有些系統工具運作上不確定會不會有點問題，
還是先單純化 asdf 工具的使用吧

## blackbox
一個用 GPG 加解密檔案的工具，可以利用 blackbox 提供的 script 去做檔案加解密，用來保護自己的一些機敏資料

## gitrc
這邊是設定 global gitconfig 的操作，主要透過 includeif 的指令去區隔不同目錄下 gitconfig 設定，
關於 git includeif 的介紹之前有文章提到過 [INCLUDEIF OF GIT CONFIG](/post/2021-05-10-git-config-includeif)
可以參考之前文章的說明

```zsh
# include zsh config
for file in "$HOME"/.zsh/**/*.zsh
do
  source "$file"
done
```

## antibody
目前使用 antibody 去作為 zsh 的 plugin 管理工具，在 zsh_plugin 中設定安裝 zsh 所需的套件，
在 theme 我是使用 p10k 的 theme，
並透過 folder 去區隔 zsh 的一些 aliases, export 和 functions 的使用，
也可以區隔工作用的 zsh 指令，
透過下面指令去 source 資料夾下的 zsh 設定

```zsh
# include zsh config
for file in "$HOME"/.zsh/**/*.zsh
do
  source "$file"
done
```

## HomeBrew
HomeBrew 是在 MacOS 上常見的第三方套件安裝工具也包含一些 GUI App 的安裝也可以使用，
Brewfile 是透過 HomeBrew bundle 備份下來的套件列表，
可以透過 HomeBrew bundle 去快速恢復之前安裝的套件

以上就是簡單介紹自己的 dotfile 設定，
希望能幫助到大家管理自己的 dotfile.




