---
title: "Activity LaunchMode"
date: 2014-11-03 17:50:23 +0800
categories:
- Android
---

最近遇到 onActivityResult 沒有反應的問題，找了些許資料後才發現原來自己忽略了 Activity 啟動的原點，這邊紀錄一下 Activity 的啟動模式

Activity 的啟動模式在 `androidManifest.xml` 中設定
```xml
<activity android:name=".MainActivity" android:launchMode="standard"</activity>
```

### Activity LaunchMode
  + **standard** : 每次 startActivity 都會產生新的 Activity Instance
  + **singleTop** : 當 startActivity 已經有相同的 Activity Instance 在佇列中，且是 Top 狀態時，不產生新的 Activity Instance
  + **singleTask** : 所有 startActivity 都會產生唯一的 Activity Instance 並存在於主 Task 中
  + **singleInstance** : 當 startActivity 後該 Activity 會存於在和主 Task 不同的 Task 中且 Activity Instance 是唯一的

### Standard Example
standard 為 Activity LaunchMode 的預設值，如果沒有設定 LaunchMode 就會以 standard 來運行
使用 Standard 運作原理如下

> `狀況題` A --start--> A  --start--> B  --start--> A  --back--> B --back--> A  
> `Queue` A A B A --back--> A A B --back--> A A

### SingleTop Example
如果當下的 Activity 不是在最上層，則會產生一個新的 Activity
使用 SingleTop 運作原理如下

> `狀況題` A --start--> A  --start--> B  --start--> A  --back--> B --back--> A  
> `Queue` A --start--> A --start--> A B --start--> A B A --back--> A B --back--> A

### SingleTask Example
同一個 Task 中只能有一個 Activity，如果在發現 Activity 的 Instance 已經存在，則清空這個 Instance 之上的 Activity
使用 SingleTask 運作原理如下

> `狀況題` A --start--> B  --start--> B  --start--> C  --start--> D  --start--> B  
> `Queue` A B --start--> A B --start--> A B C --start--> A B C D --start--> A B

### SingleInstance Example
Activity 用一個新的 Task 來裝載啟動，而且保證不再有其他 Activity Instance 進入
使用 SingleInstance 運作原理如下
> A => standard  
> B => singleInstance  
> 開啟順序為 A(task1) --start--> B(task2) --start--> A(task1)  
> 當按 Back 鍵時 A(task1) --back--> A(task1) --back--> B(task2)  


### onActivityResult 調用的時機
  1. 在 standard, singleTop 兩個模式下 onActivityResult 方法會在當前的 Activity 結束掉後被調用
  2. 在 singleTask, singleInstance 兩個模式下 onActivityResult 方法會立即調用，等到再 setResult 的時候就不再作用了

> setResult 方法與 onActivityResult 方法是不同步的，使用上要多加注意
