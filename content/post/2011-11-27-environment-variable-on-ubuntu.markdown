---
title: "Environment Variable on Ubuntu"
date: 2011-11-27 17:37:00 +0800
categories:
- Ubuntu
---

# Profile & Environment Difference
+ /etc/profile  
  設定所有使用者環境變數的檔案
+ /etc/environment  
  設定本機系统環境變數的檔案

當進入登入畫面時，系統先取得 environment 裡的環境變數執行
<pre class="prettyprint text">
  /etc/environment
</pre>

當登錄系統時 shell 讀取環境變數的順序是 :

> /etc/profile  =>  /etc/environment  =>  $HOME/.profile

#### 如果同一個環境變數在使用者環境變數( /etc/profile )和系统環境變數( /etc/environment )有不同的值那應該以使用者的環境變數為主
<p></p>

# Environment Variable Document Difference
1. #### /etc/profile :  
此文件為系統的每個用戶設置環境信息，當用戶第一次登錄時，該文件被執行，並從 /etc/profile.d 目錄的配置文件中蒐集 shell 的設置
2. #### /etc/bashrc :  
此文件為每一個運行 bash shell 的用戶執行此文件，當 bash shell 被打開時，該文件被讀入
3. #### ~/.bash_profile :  
每個用戶都可使用該文件輸入專用於自己使用的 shell 信息，當用戶登錄時，該文件僅僅執行一次，默認情況下，他設置一些環境變數，執行用戶的 .bashrc 文件
4. #### ~/.bashrc :  
此文件包含專用於使用者的 bash shell 的 bash 信息，當登錄時以及每次打開新的 shell 時，則該文件被讀取
5. #### ~/.bash_logout :  
當每次退出系統(退出bash shell)時，執行該文件，另外 /etc/profile 中設定的變數可以作用於任何用戶，而 ~/.bashrc 等中設定的變數只能繼承 /etc/profile 中的變數，他們是父子關係
6. #### ~/.bash_profile :  
此文件是交互式 login 方式進入 bash 運行的， ~/.bashrc 是交互式 non-login 方式進入 bash 運行的通常二者設置大致相同，所以通常前者會調用後者
<p></p>

# Conclusion
環境變數盡量不要設定在 environment 檔裡面，主要原因為 environment 檔為系統環境主要設定檔案，因此如果發生錯誤可能會造成系統死當，如果設定為所有使用者皆使用的環境變數，應設定在 profile 檔裡面，如果個別使用者要使用的環境變數則設定在 .bashrc 較為恰當
<pre class="prettyprint text">
JAVA SDK 環境變數 -> /etc/profile
Android SDK -> ~/.bashrc
Android NDK -> ~/.bashrc
</pre>
