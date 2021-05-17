---
title: "Error : ADB Server didn't ACK on Windows"
date: 2013-11-28 23:40:27 +0800
categories:
- Android
- Eclipse
---

# Outline
產生ADB Server didn't ACK 問題的主要原因為 ADB Server 的 Port 绑定失败，因此只需要關閉 Adb Server 所需綁定的 Port 上佔用的程序即可；我是在使用 Windows 系統時，自動啟動的 QQ 即時通訊軟體使 ADB Server 需綁定的 Port 被佔用，因此產生此問題
<p></p>

## Quit Eclipse & Open Command Line
關閉 Eclipse 使 android 的 adb與Eclipse 間的連結關閉，並打開 commad line ，切換目錄到 Android SDK 目錄中 platform-tools 資料夾裡
>切換到 platform-tools 資料夾中是為了使用 adb 工具，如果有有將 platform-tools 資料夾路徑加到環境變數中可以不需要切換

<pre class="prettyprint text">
$D:\Application\adt-bundle-windows\sdk\platform-tools > dir
NOTICE.txt  adb  api  fastboot  source.properties  systrace
</pre>

## Turn Off ADB Server & Turn On ADB Server Again
關閉並重新啟動 ADB Server
<pre class="prettyprint text">
$ adb kill-server
$ adb start-server
#啟動服務成功顯示畫面如下
* daemon not running. starting it now on port 5037 *
* daemon started successfully *
#啟動服務失敗顯示畫面如下
* daemon not running. starting it now *
* ADB server didn't ACK *
* failed to start daemon *
</pre>

## IF Failed To Start ADB Server
如果前面步驟還沒成功啓動 ADB Server ，則需要查詢有哪些程序佔用了 ADB Server 的 port
>ADB Server 預設Port爲5037

<pre class="prettyprint text">
$ netstat -ano | findstr "5037"
TCP    127.0.0.1:1159         127.0.0.1:5037         ESTABLISHED     2492
TCP    127.0.0.1:5037         0.0.0.0:0              LISTENING       2796
TCP    127.0.0.1:5037         127.0.0.1:1159         ESTABLISHED     2796
TCP    127.0.0.1:5037         127.0.0.1:1349         TIME_WAIT       0
TCP    127.0.0.1:5037         127.0.0.1:1350         TIME_WAIT       0
TCP    127.0.0.1:5037         127.0.0.1:1353         TIME_WAIT       0
</pre>

## Kill Task & Start ADB Server
如果有查詢到佔用的 5037 port 的 Task，則用 taskkill 指令根據 PID 刪除它；並重新啟動 ADB Server
<pre class="prettyprint text">
$ taskkill /F /PID 2796
$ taskkill /F /PID 2492
$ adb start-server
* daemon not running. starting it now on port 5037 *
* daemon started successfully *
</pre>
