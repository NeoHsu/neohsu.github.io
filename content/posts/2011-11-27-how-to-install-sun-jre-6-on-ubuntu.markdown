---
title: "How to Install Sun JRE 6 On Ubuntu"
date: 2011-11-27 15:10
categories:
- Ubuntu
- JAVA
---

# Outline
+ JRE 是建置 JAVA 執行環境的軟體
+ JRE 的內部有一個 Java 虛擬機器（JVM）以及一些標準的類別函數庫，協助 Java 程式運作
+ JRE 只能運行 JAVA 程式，無法開發 JAVA 程式
<p></p>

# Development environment
* Ubuntu 11.10 (x86-64)
* Dell Studio XPS 1640
<p></p>

# Method 1: Install a package by a PPA
1.加入新的的PPA來源
<pre class="prettyprint text">
$ sudo add-apt-repository ppa:ferramroberto/java
$ sudo aptitude update
</pre>
2.Install Sun JRE & Java Plugin
<pre class="prettyprint text">
$ sudo aptitude install sun-java6-jre sun-java6-Plugin sun-java6-fonts
</pre>

# Method 2: Installing the JRE manually
1.建立你要安裝的目錄並到該目錄下
<pre class="prettyprint text">
$ mkdir -p ~/programs
$ cd ~/programs
</pre>

2.Downloading JRE from the [Oracle Web site ](http://www.oracle.com/technetwork/java/javase/downloads/index.html),The file is bin file.
<pre class="prettyprint text">
$ wget http://download.oracle.com/otn-pub/java/jdk/6u29-b11/jre-6u29-linux-x64.bin
</pre>

3.將下載的 JRE 設定為可執行的檔案並安裝
<pre class="prettyprint text">
$ chmod +x jre-6u29-linux-x64.bin
$ ./jre-6u29-linux-x64.bin
</pre>

執行完後會在目錄下建立一個資料夾（jre1.6.0）這個資料夾是你安裝 JRE 的路徑

4.在 /etc/profile 檔案中設定環境變數
<pre class="prettyprint text">
$ sudo vim /etc/profile
</pre>
在設定環境變數的檔案中加入下面參數
{% codeblock profile %}
export JAVA_HOME="/home/simonhsu/programs/jre1.6.0_29"
export CLASSPATH=.:/JAVA_HOME/lib
export PATH=$PATH:$JAVA_HOME/bin
{% endcodeblock %}

5.重新載入 /etc/profile 並重新開機
<pre class="prettyprint text">
$ sudo source /etc/profile
$ sudo init 6
</pre>

#### These are the steps to install a JRE on your system.
<p></p>
# If you need installed plugin to browser:

#### Firefox:

1.建立瀏覽器的 Plugin 資料夾
<pre class="prettyprint text">
$ mkdir -p ~/.mozilla/plugins
</pre>
2.建立Plugin的連結
<pre class="prettyprint text">
$ ln -s ~/programs/jre1.6.0_29/lib/amd64/libnpjp2.so ~/.mozilla/plugins/
</pre>

#### Chrome:

1.建立Chrome瀏覽器的 Plugin 資料夾
<pre class="prettyprint text">
$ mkdir -p /opt/google/chrome/plugins
</pre>
2.建立Plugin的連結
<pre class="prettyprint text">
$ ln -s ~/programs/jre1.6.0_29/lib/amd64/libnpjp2.so /opt/google/chrome/plugins
</pre>

#Test JRE & Plugin

> Test Java Runtime Environment
<pre class="prettyprint text">
$ java -version
java version "1.6.0_29"
Java(TM) SE Runtime Environment (build 1.6.0_29-b11)
Java HotSpot(TM) 64-Bit Server VM (build 20.4-b02, mixed mode)
</pre>
> Test Browser Plugin

Go [How do I test whether Java is working on my computer?](http://java.com/en/download/testjava.jsp) website can test JRE Plugin
