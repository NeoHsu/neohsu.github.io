---
title: "Talk About View Component On Android"
date: 2013-12-11 22:51:29 +0800
categories: 
- Android
---

{% img /images/SVG/1-talk-about-view-component-on-android.svg %}

在 Android 開發中，我認為了解 View 的執行過程有助於提高介面呈現的效率，可以幫助一些複雜 Layout 在適當的時機中處理，或是可以減少不必要的畫面重製，這些都是對於 View 的執行過程有很大的關聯

Android 中 View 的呈現主要經過 Mesure, Layout 和 Draw 這三個過程：

## Mesure
此階段主要是負責計算 view 佔用的大小，簡單的說就是將 match_parent 和 wrap_content 轉成目前顯示的實際大小；透過 onMeasure(widthMeasureSpec, heightMeasureSpec) 方法去處理計算 View 在目前螢幕上顯示的區塊大小
> View 的大小是有 Parent View 和自己的大小决定而不是單一個 View 來决定，因此需要配合自己的 Sub-View 去做計算調整

## Layout
當 View 配置好大小後，便讀取設定好的 Layout 配置，將 Layout 佈局放到建立好的 View 中並計算出 View 在螢幕上的座標；透過 onLayout(int l, int t, int r, int b )方法去處理
> 處理組件的佈局通常要重寫 onMesure 和 onLayout 這兩個方法

## Draw
Draw 過程中主要是先畫 Drawable 背景，需注意的是背景 Drawable 的實際大小會影響 View 的大小； Draw 過程會調用 onDraw(Canvas canvas) 方法去繪製背景，之後再透過 dispatchDraw(Canvas canvas) 方法分發給 Sub-View 進行繪製，值得注意的是 ViewGroup 在繪製時，如果它沒有背景則直接調用的是 dispatchDraw() 方法，繞過了 draw() 方法，當它有背景的時候就調用 draw() 方法，而 draw() 方法裡包含了 dispatchDraw() 方法的調用
> 自定組件時通常是重寫 onDraw() 方法，而在 ViewGroup 上繪製東西的時候往往重寫的是 dispatchDraw() 方法

## Summary
Android 中所有畫面都是用 Canvas 根據座標去畫出來的結果，因此每次當 View 改變的時候，需要再去重新計算與繪製一個 View 來產生出畫面來

可想而知在 ListView 中，我們不斷地 onScroll 時，如果沒做一些效能處理與緩存的處理，它的運作模式將會是不斷地重新繪製畫面；因為當每次移動 Scroll 時都會進行 getView 的動作，之後便開始一連串產生 View 的行為
