---
title: Using time zone with golang on scratch
date: 2021-08-25T15:00:30Z
categories:
- Golang
- Docker
---

在建置 Golang 的 microservices 時，我們常常為了最小化而使用了 scratch image，  
這主要也是 Golang 的編譯特性導致，我們只需要編譯出執行檔，就可以在 scratch 這樣一個空殼的 docker image 上進行運作，  
但往往有時候我們會漏掉需要建置一些系統上需要的資訊 (SSL Certificate, TimeZone...)，  
導致整個 Golang 編譯出來的服務因為找不到對應的資訊而無法正常執行，  
下面就來說明一下 Golang 在運作 time library 時，針對 TimeZone 的處理方式，以及如何在 scratch 上加入 TimeZone 的資訊吧！

我們先開發一個使用到 TimeZone 相關的小程式
```Golang
package main

import (
	"fmt"
	"time"
)

func main() {
	timeZone, _ := time.LoadLocation("Asia/Taipei")
	now := time.Now().UTC().In(timeZone)
	fmt.Println(now.String())
}
```

上面是簡單的範例，主要是設定 location 後印出當地的現在時間，  
執行後會呈現下方的時間資訊
```s
❯ go run main.go
2021-08-26 00:06:31.131941 +0800 CST
```

我們將該程式包入最小化 docker image 中
```dockerfile
FROM golang:1.17-alpine AS builder
WORKDIR /app
COPY . .
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -ldflags="-w -s" -o demo main.go

FROM scratch 
COPY --from=builder /app/demo /demo
ENTRYPOINT ["/demo"]
```

當我們根據 Dockerfile 編譯出最小化的 docker image 後，試著去執行看看會發生怎樣的情況
```s
❯ docker run demo:dev
panic: time: missing Location in call to Time.In

goroutine 1 [running]:
time.Time.In(...)
        /usr/local/go/src/time/time.go:1106
main.main()
        /app/main.go:10 +0x165
```

上面錯誤表示無法找到對應的 TimeZone 資訊，  
相當然爾我們使用了 scratch 去打包裡面只有我們的編譯執行檔，所以沒有 TimeZone 資訊，    
這時候就有點好奇 Golang 怎麼去取得這個 TimeZone 的資訊呢?

在 [Golang 的 source code](https://golang.org/src/time/zoneinfo_unix.go) 中可以知道 Golang 在 Unix 上取得 TimeZone source 的路徑，
```Golang
// Many systems use /usr/share/zoneinfo, Solaris 2 has
// /usr/share/lib/zoneinfo, IRIX 6 has /usr/lib/locale/TZ.
var zoneSources = []string{
	"/usr/share/zoneinfo/",
	"/usr/share/lib/zoneinfo/",
	"/usr/lib/locale/TZ/",
	runtime.GOROOT() + "/lib/time/zoneinfo.zip",
}
```

從上面看到 source path 並沒有在 docker scratch image 上被設定，  
我們可以在對應位置中放入對應的 timezone info 可以解決這樣的問題，  
而我們再更上層一點的 [Golang zoneinfo 的 source code ](https://golang.org/src/time/zoneinfo.go) 中，  
我們可以知道 Golang 會預設 **ENV ZONEINFO** 指定路徑為 **$GOROOT/lib/time/zoneinfo.zip**，  
**zoneinfo.zip** 是 Golang 自帶的 TimeZone 資訊，主要放在 time library 中，  
因此我們可以達到一些解決方案:
1. 將 **zoneinfo** 放到上面 **zoneSources** 提到的對應位置中
2. 直接使用 **ZONEINFO** 環境變數指定 Golang 提供的 **zoneinfo.zip** 檔案來處理

最後我這邊選擇將 Golang 自帶的 **zoneinfo.zip** 放到環境變數對應的位置中，  
只需將 Dockerfile 做最簡單的修正即可達到我們的目的
```dockerfile
FROM golang:1.17-alpine AS builder
WORKDIR /app
COPY . .
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -ldflags="-w -s" -o demo main.go

FROM scratch 
COPY --from=builder /app/demo /demo
COPY --from=builder /usr/local/go/lib/time/zoneinfo.zip /zoneinfo.zip
ENV ZONEINFO=/zoneinfo.zip
ENTRYPOINT ["/demo"]
```

讓我們來運行一下是否可以正常 work
```s
❯ docker run demo:zoneinfo
2021-08-26 00:40:24.7861271 +0800 CST
```