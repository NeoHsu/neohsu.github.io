---
title: "Strings Split of Go"
date: 2021-05-13T15:10:49Z
categories:
- Golang
- Strings
---
最近遇到用 Golang 將字串拆解成 slices 的小陷阱，  
在開發上大家有時候會需要拆解字串變成 slices 的應用，  
例如 input 資料是一串文章的分類 **"Golang,Docker,Kubernetes,Nats"**，我們希望 output 一個分類的 slices 之後再做處理運算，  

```Golang
input := "Golang,Docker,Kubernetes,Nats"
categories := strings.Split(input, ",")
fmt.Println(len(categories))
```

此段程式運作後會印出 4，表示從 input 字串中拆解出 4 個 string 的 slices，  
感覺上很直覺但是陷阱也在之中，

如果上面例子我們將 input 賦予空字串後，會產生怎樣的結果呢？

```Golang
input := ""
categories := strings.Split(input, ",")
fmt.Println(len(categories))
```

最後得到的輸出卻是 1 !!!  
明明沒有資料為什麼還會給予一個空字串的 slices 呢？

跑去看了一下 [Golang 官方文件](https://golang.org/pkg/strings/#Split) 說明


> Split slices s into all substrings separated by sep and returns a slice of the substrings between those separators.
>
> If s does not contain sep and sep is not empty, Split returns a slice of length 1 whose only element is s.
>
> If sep is empty, Split splits after each UTF-8 sequence. If both s and sep are empty, Split returns an empty slice.
>
> It is equivalent to SplitN with a count of -1.

原來當 **sep** 是空的時候他會回傳一個 **空字串的 slice**，  
追了一下 source code 主要是 **make** 了一個 **[]string{}** 的 slice 導致，

```Golang
// Generic split: splits after each instance of sep,
// including sepSave bytes of sep in the subarrays.
func genSplit(s, sep string, sepSave, n int) []string {
	if n == 0 {
		return nil
	}
	if sep == "" {
		return explode(s, n)
	}
	if n < 0 {
		n = Count(s, sep) + 1
	}

	a := make([]string, n)
	n--
	i := 0
	for i < n {
		m := Index(s, sep)
		if m < 0 {
			break
		}
		a[i] = s[:m+sepSave]
		s = s[m+len(sep):]
		i++
	}
	a[i] = s
	return a[:i+1]
}
```

上面過程可以知道當 input 是空的時候他會 make 一塊空字串的 slice 去準備將分割後字串塞入，  
發現這問題主要是我們在用 gRPC 傳送資料的時候，明明是空的 input 卻還是有接收到對應的參數被傳入，  
這小地方沒特別注意還真的會誤用啊，  
提供給有需要使用 `strings.Split` 的人可以留意一下喔！

