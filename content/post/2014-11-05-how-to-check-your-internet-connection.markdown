---
title: "How to check your internet connection"
date: 2014-11-05 18:13:58 +0800
categories:
- Android
---

查詢目前的手機上，是否有網路連線並判斷目前連線的模式，有很多方式可以去做查詢，下面是很多人常用的方式

<pre class="prettyprint java">
public static String checkNetworkConnection(Context context) {
  ConnectivityManager manager = (ConnectivityManager)context
                    .getSystemService(Context.CONNECTIVITY_SERVICE);
  NetworkInfo netInfo = manager.getActiveNetworkInfo();
  if (netInfo != null && netInfo.isConnectedOrConnecting()){
    State mobile = manager.getNetworkInfo(ConnectivityManager.TYPE_MOBILE)
        .getState();
    State wifi = manager.getNetworkInfo(ConnectivityManager.TYPE_WIFI)
        .getState();
    if (mobile == State.CONNECTED || mobile == State.CONNECTING)
      return "TYPE_MOBILE";
    if (wifi == State.CONNECTED || wifi == State.CONNECTING)
      return "TYPE_WIFI";
  }else{
    return null;
  }
}
</pre>

上方程式區塊可以查詢到目前網路是否連線並確認連線模式 ( 3G / Wifi ) 後，變回傳相對應的值給 Caller

但是最近在 Nexus 7 上運行該功能時發生了 Crash 的狀況

> 主要原因是在目前 Android 平板裝置上，有些裝置不一定有配置 3G 的模組，因此會在運行到 **ConnectivityManager.TYPE_MOBILE** 時，因為找不到相關資訊而導致 Crash

目前我想到的解決方式只能用改變順序的方式去避過這樣的錯誤，靠著先判斷網路連線和 Wifi 狀況而不去使用 TYPE_MOBILE 模式來偵測

<pre class="prettyprint java">
public static String checkNetworkConnection(Context context) {
  ConnectivityManager manager = (ConnectivityManager) mCtx
                    .getSystemService(Context.CONNECTIVITY_SERVICE);
  NetworkInfo netInfo = manager.getActiveNetworkInfo();
  if (netInfo != null && netInfo.isConnectedOrConnecting()){
    State wifi = manager.getNetworkInfo(ConnectivityManager.TYPE_WIFI).getState();
    if (wifi == State.CONNECTED || wifi == State.CONNECTING) {
      return "TYPE_WIFI";
    }else{
      return "TYPE_MOBILE";
    }
  }else{
    return null;
  }
}
</pre>

> 感覺改變順序做法上有點難看，但是還是可以解一下燃眉之急啊！但是由上面例子可以讓我們重新思考，是不是有些例外處理自己沒有做好呢？真改好好反省一下...
