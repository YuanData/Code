---
title: "Channel"
draft: false
slug: "class-15"
series: ["Go基礎入門"]
series_order: 15
---
本節課說明channel，是 Go 並行處理的核心元素。

## 什麼是Channel
- Channel是Go語言中一種特殊的數據類型，用於在Goroutines之間傳遞數據。
- 它類似於一個數據流管道：數據在一端進入，在另一端被取出。

## 基本操作與宣告
- 宣告一個Channel通常用`chan`關鍵字，例如`c1 := make(chan int)`。
- `make`函式用於初始化Channel，可指定數據類型和選擇性的緩衝區大小。

## 緩衝區與阻塞
- 無緩衝區的Channel在數據傳輸時會阻塞，直到另一端準備好接收。
- 有緩衝區的Channel允許數據存儲在緩衝區中，減少阻塞的發生。

## 數據傳輸
- 數據的傳輸使用`<-`操作符，例如`c1 <- 1`將數字1發送到Channel `c1`。

## 接收與關閉
- 使用`<-`操作符接收數據，如`value := <-c1`。
- 使用`close`函式可以關閉Channel，防止發生死鎖。

## Channel與Goroutine的結合使用
- Channel經常與Goroutine結合使用，以實現並發處理。
- 使用select-case結構可有效管理多個Channel的讀寫操作。

## 實際範例：網絡請求處理器
這個範例展示了如何使用Channel和Goroutine來並行處理多個網絡請求。每個請求都在自己的Goroutine中運行，並將響應時間發送到共享的Channel。
```go
package main

import (
	"fmt"
	"net/http"
	"time"
)

func fetchURL(url string, ch chan<- string) {
	start := time.Now()
	resp, err := http.Get(url)
	if err != nil {
		ch <- fmt.Sprint(err)
		return
	}
	defer resp.Body.Close()
	ch <- fmt.Sprintf("%s responded in %v", url, time.Since(start))
}

func main() {
	urls := []string{
		"https://www.google.com",
		"https://www.facebook.com",
		"https://www.amazon.com",
	}

	ch := make(chan string)

	for _, url := range urls {
		go fetchURL(url, ch)
	}

	for range urls {
		fmt.Println(<-ch)
	}
}
```
Channel作為Goroutines間的溝通管道，進行數據傳輸、管理阻塞和緩衝區，特別是在處理並行任務，如網絡請求時，Channel的使用顯得尤為重要，也為寫出更高效、更可靠的Go應用程序奠定了基礎。