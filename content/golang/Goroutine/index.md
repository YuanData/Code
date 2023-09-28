---
title: "Goroutine"
draft: false
slug: "class-14"
series: ["Go基礎入門"]
series_order: 14
---
本節課介紹Goroutine是Go實現高效並發的輕量級執行緒。

## Goroutine的基本概念
- Goroutine是Go語言中實現輕量級並發的關鍵機制。
- 它允許同時執行多個任務，使得應用程序可以高效處理多個操作。

## Goroutine的重要性
- 主執行緒（main）的結束會中止所有Goroutine。
- 正確管理Goroutine的生命週期和同步是確保高效並發的關鍵。

## Goroutine的實際應用場景

### 範例：網絡請求的並發處理
在開發中，我們經常需要同時發起多個獨立的網絡請求，比如，從不同的API同時獲取數據。使用Goroutine可以有效地實現這一目的。

```go
package main

import (
    "fmt"
    "net/http"
    "sync"
)

// fetch 用於發起HTTP請求並印出狀態碼
func fetch(url string, wg *sync.WaitGroup) {
    defer wg.Done()

    resp, err := http.Get(url)
    if err != nil {
        fmt.Println("Error fetching URL:", url, err)
        return
    }
    fmt.Println("URL:", url, "Status Code:", resp.StatusCode)
    resp.Body.Close()
}

func main() {
    var wg sync.WaitGroup
    urls := []string{
        "https://www.google.com",
        "https://www.apple.com",
        "https://www.amazon.com",
    }

    for _, url := range urls {
        wg.Add(1)
        go fetch(url, &wg)
    }

    wg.Wait()
    fmt.Println("All requests finished.")
}
```
這個範例展示了如何使用Goroutine來並行處理多個網絡請求。在現代Web開發和微服務架構中，這樣的應用非常常見。使用Goroutine能夠顯著提高網絡請求的處理效率，減少等待時間。此外，通過`sync.WaitGroup`確保了所有請求都完成後，主執行緒才結束，從而避免了程序提前退出的問題。