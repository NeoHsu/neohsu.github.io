---
title: "IncludeIf of Git config"
date: 2021-05-10 17:00:00 +0800
categories:
- Git
---

不知道大家怎麼管理自己不同 gitconfig 的設定呢？  
舉例來說通常我們 `git clone` 後，要在修改後的 code 做 `git commit` 以前都需要做下列設定

```
$ git config user.name "Neo Hsu"
$ git config user.email "NeoHsuDroid@gmail.com"
```

該指令會在我們 `git clone` repo 的目錄內設定 gitconfig 的 name, email，  
我們在設定了基本資訊後才能做 `git commit` 的後續動作，  
但當我們要抓多個 repo 下來時，每次都要做這動作感覺很費時又多指令，    
因此很多使用者會將該 gitconfig 設定成 global 來減少各自設定 gitconfig 的操作

```
$ git config --global user.name "Neo Hsu"
$ git config --global user.email "NeoHsuDroid@gmail.com"
```

也許上面的作法可以暫時解決了每次 git clone 下來後不用再次設定的問題，  
但如果你擁有兩個 gitconfig 要做切換時又該怎麼處理呢，  
舉例來說目前你需要設定兩組 git config，   
- personal gitconfig  
- company gitconfig  

某些 repo 需要各別設定 personal gitconfig 或 company gitconfig 的狀況下，  
你是否會選擇性的在某些 repo 做針對該 repo 設定 gitconfig 的操作呢？

以前我會利用 [autoenv](https://github.com/inishchith/autoenv) 工具來處理，  
[autoenv](https://github.com/inishchith/autoenv) 提供切換到的目錄下會先去執行 .env 檔案的內容，  
藉由這個特性去避免需要主動設定執行 `git config` 的操作，    
不過 [autoenv](https://github.com/inishchith/autoenv) 需要在指定的目錄中另外設定 .env 檔案，  
且需要主動切換到該目錄下才會針對 .env 檔生效該設定，
其實這樣做法有點不合理，  
需要另外在設定用別的檔案來做 git config 操作，  
有點脫褲子放屁的感覺...

最近看了一下 [git 官方文件](https://git-scm.com/docs)，  
發現一個新的 `git config` 的 [IncludeIf](https://git-scm.com/docs/git-config#_conditional_includes) 參數設定，  
該設定可以針對不同的資料夾目錄去 include 不同的 gitconfig，  
主要在 global gitconfig 做設定即可，感覺上蠻符合前面問題的解決方案，  
設定方式如下：  

**global gitconfig**
```
[includeIf "gitdir:~/Documents/Code/Personal/"]
    path = ~/.gitrc/personal.gitconfig
```

**personal.gitconfig**
```
[user]
    name = "Neo Hsu"
    email = "NeoHsuDroid@gmail.com"
    signingkey = "xxxxxxxxxxxxxxx"
```

上面設定會將在 `~/Documents/Code/Personal/` 目錄下有 .git 做版本控制的資料夾給予 `personal.gitconfig` 的設定


### Reference
> [git-config](https://git-scm.com/docs/git-config#_conditional_includes)

