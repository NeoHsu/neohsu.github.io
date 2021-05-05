---
title: "Talk About ListView Adapter on Android"
date: 2013-12-12 23:02:24 +0800
categories: 
- Android
---

在 Android 開發過程中，ListView 和 Adapter 有著密不可分的關係；Adapter 主要負責 Data 和 ListView 之間資料配置與 Layout 佈局的媒介，因此在實作 Custom ListView 時，常會針對 Adapter 組件去做客製化的設計；下面將介紹 Adapter 和 ListView 之間的運作原理並說明不同資料形態的 Adapter 運作模式
<p></p>
# ListView & Adapter

![talk-about-listview-and-adapter-on-android](/images/0-talk-about-listview-and-adapter-on-android.png)

一般而言 ListView 是由以下三個元素所组成，  

1. View：  
用來顯示 ListView 中每一個 Item 所要呈現的配置
2. Adapter：  
將不同的資料對應到 View 中，不同的資料使用不同的 Adapter 去做對應，如 ArrayAdapter，CursorAdapter，SimpleAdapter 等，Adapter 會將資料對應到 ListView Item 的 View 中來呈現資料
3. Data：  
Adapter 所需要對應的資料來源，資料形態可以是字串或圖片，儲存資料的媒介可以是 ArrayList 或 Cursor 並透過 Adapter 將資料放到 Item 中；所有的資料會顯示到 ListView 上都是透過 Adapter 來完成

根據上圖我們可以知道當使用 ListView 時，我們會先將要放入 ListView 的資料丟入 Adapter 中， Adapter 透過 XML 中 Layout 的佈局去配置每個 Item 的樣式與資料，最後再將配置好的佈局放入 ListView 中呈現出我們所設定的樣式；事實上系統已有預設的 Adapter 可以運用，但有時候並不能滿足我們的需求，因此要實現複雜的 ListView 可以透過繼承 Adapter 並重寫相對應的方法即可；以下將介紹一下透過不同 Adapter 的運作過程
<p></p>

# BaseAdapter & CursorAdapter

我們知道如果要實作較為複雜的 ListView 需要去針對 Adapter 去做修改；以下將介紹透過不同 Adapter 去操控 ListView 的運作過程

> BaseAdapter：通用的Adapter 讓你可以處理任何你想要的動作，但是必須做更多的編碼去處理相對應事件  

> ArrayAdapter：BaseAdapter 的擴展，它可以用在資料形態為 ArrayList 的情況  

> CursorAdapter：BaseAdapter 的擴展，主要用於當資料形態是 Cursor 的情況

## BaseAdapter

![talk-about-listview-and-adapter-on-android](/images/1-talk-about-listview-and-adapter-on-android.svg)

根據上圖我們可以知道 BaseAdapter 的運作模式，當 ListView 受到 Scroll 事件響應的時候，ListView 會先調用 getCount 去和後端資料取得 Item 個數，並調用 getView 方法去繪製每個要呈現的 Item View ；如果要取得某一列的內容就會調用 getItem 這個方法；如要取得某一列的 Id 就可以調用 getItemId 方法去實現

## CursorAdapter

![talk-about-listview-and-adapter-on-android](/images/2-talk-about-listview-and-adapter-on-android.svg)

由上圖可以看出使用 CursorAdapter 時，當 ListView 產生 Scroll 事件後，會去取得目前 Cursor 位置，如果顯示的 Item 需要被繪製 View 的時候，會調用 newView 去繪製 Item ，如果不需要重新繪製則調用 bindView 去做顯示的動作

因此我們可以知道在 CursorAdapter 中 ListView 不是每次都會調用 newView ，所以每個 Item View 只會被實體化一次。而每次要繪製 Item 之前一定會調用 bindView ；如果記錄被修改了在 ListView 中也會被更新，這種方式是動態綁定資料。如果要實現資料的延遲加載，可以在 bindView 中先加載資料，再啟動 Thread 更新資料

## Summary
由上面我們可以知道整個 ListView & Adaper 呈現的運作模式與相互關係，在這邊我們可以發現其實在整個 Adapter 的做法中，當我們不斷地在進行 Scroll 動作時，它會不斷地調用 getView 的動作，導致系統不斷的建立 View ，不斷的將畫面繪製在螢幕上，因此會導致手機的暫存空間不足導致程式閃退問題

