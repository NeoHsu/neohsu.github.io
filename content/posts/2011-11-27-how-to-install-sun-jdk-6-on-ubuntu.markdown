---
title: "How to install Sun JDK 6 On Ubuntu"
date: 2011-11-27 00:52
categories:
- Ubuntu
- Java
---

# Outline
+ JDK 提供開發人員的軟體開發工具並建置編譯與執行 JAVA 的開發環境
+ JDK 包含了 JAVA 開發工具外，還包含了 JRE 和 JVM
+ JDK 可以開發與運行 JAVA 程式
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

2.Install Sun JDK & Java Plugin
<pre class="prettyprint text">
$ sudo aptitude install sun-java6-jdk sun-java6-plugin sun-java6-fonts
</pre>

# Method 2: Installing the JDK manually

1.建立你的安裝的目錄並到該目錄下
<pre class="prettyprint text">
$ mkdir -p ~/programs
$ cd ~/programs
</pre>

2.Downloading JDK from the [Oracle Web site ](http://www.oracle.com/technetwork/java/javase/downloads/index.html),The file is bin file.
<pre class="prettyprint text">
$ wget http://download.oracle.com/otn-pub/java/jdk/6u29-b11/jdk-6u29-linux-x64.bin
</pre>

3.將下載的 JDK 設定為可執行檔案並安裝
<pre class="prettyprint text">
$ chmod +x jdk-6u29-linux-x64.bin
$ ./jdk-6u29-linux-x64.bin
</pre>

執行完後會在目錄下建立一個資料夾（jdk1.6.0）這個資料夾是你安裝 JDK 的路徑

4.在 /etc/profile 檔案中設定環境變數
<pre class="prettyprint text">
$ sudo vim /etc/profile
</pre>
在設定環境變數的檔案中加入下面參數
<pre class="prettyprint text">
export JAVA_HOME="/home/simonhsu/programs/jdk1.6.0_29"
export CLASSPATH=.:/JAVA_HOME/lib
export PATH=$PATH:$JAVA_HOME/bin
</pre>

5.重新載入 /etc/profile 並重新開機
<pre class="prettyprint text">
$ sudo source /etc/profile
$ sudo init 6
</pre>

#### These are the steps to install a JDK on your system.
<p></p>

# If you need installed the plugin to browser:

#### Firefox:

1.建立瀏覽器的 Plugin 資料夾
<pre class="prettyprint text">
$ mkdir -p ~/.mozilla/plugins
</pre>

2.建立Plugin的連結
<pre class="prettyprint text">
$ ln -s ~/programs/jdk1.6.0_29/jre/lib/amd64/libnpjp2.so ~/.mozilla/plugins/
</pre>

#### Chrome:

1.建立Chrome瀏覽器的 Plugin 資料夾
<pre class="prettyprint text">
$ mkdir -p /opt/google/chrome/plugins
</pre>

2.建立Plugin的連結
<pre class="prettyprint text">
$ ln -s ~/programs/jdk1.6.0_29/jre/lib/amd64/libnpjp2.so /opt/google/chrome/plugins
</pre>

# Test JDK & Plugin

> Test Java Development Kit
<pre class="prettyprint text">
$ java -version
java version "1.6.0_29"
Java(TM) SE Runtime Environment (build 1.6.0_29-b11)
Java HotSpot(TM) 64-Bit Server VM (build 20.4-b02, mixed mode)

$ javac -version
javac 1.6.0_29
</pre>
> Test Browser Plugin

Go [How do I test whether Java is working on my computer?](http://java.com/en/download/testjava.jsp) website can test JDK Plugin
