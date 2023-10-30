---
title: "單元測試"
draft: false
slug: "class-17"
series: ["Go基礎入門"]
series_order: 17
date: "2082-07-07"
---
17: 本節課說明 Go 單元測試的基本概念、執行方法，並介紹基準測試來評估程式性能。

# 單元測試

## 單元測試的重要性
- 單元測試對於保證程式碼質量和功能正確性非常重要，尤其在嚴謹的項目和企業環境中。

## 單元測試的基本概念
- 單元測試主要針對函數的功能進行測試。
- 測試函數通常以 `Test` 開頭，並接受一個 `*testing.T` 參數。

### 示例：編寫單元測試
```go
package main

import (
	"testing"
)

func IsNotNegative(num int) bool {
	return num >= 0
}

func TestIsNotNegative(t *testing.T) {
	if !IsNotNegative(-1) {
		t.Error("負數錯誤檢測失敗")
	}
	if !IsNotNegative(1) {
		t.Error("正數錯誤檢測失敗")
	}
}
```

## 單元測試的執行
- 使用 `go test` 命令運行測試。
- 支援多種命令行標籤，如 `-v` 顯示詳細輸出，`-run` 指定特定測試函數。

# Benchmark

## 基準測試的目的
- 基準測試（Benchmark）用於測量函數的性能，特別是在處理速度和資源使用上。

## 基準測試的實現方式
- 基準測試函數以 `Benchmark` 開頭，並接受一個 `*testing.B` 參數。
- 使用 `b.N` 進行循環，以測量函數在多次執行下的平均性能。

### 示例：編寫基準測試
假設我們有一個計算斐波那契數列的函數，我們想要測試其性能：

```go
package main

import (
	"testing"
)

// Fibonacci 函數計算斐波那契數列的第 n 項
func Fibonacci(n int) int {
	if n <= 1 {
		return n
	}
	return Fibonacci(n-1) + Fibonacci(n-2)
}

// BenchmarkFibonacci 進行基準測試
func BenchmarkFibonacci(b *testing.B) {
	for i := 0; i < b.N; i++ {
		Fibonacci(10) // 測試計算斐波那契數列的第 10 項
	}
}
```
## 執行基準測試
- 使用 `go test -bench=.` 命令來執行基準測試。
- 可以配合 `-benchtime` 和 `-count` 標籤來調整測試的執行時間和次數。

## 基準測試的應用
- 基準測試有助於識別程式碼的瓶頸，進行性能優化。
- 可以用於比較不同算法或實現方式的效率。

---
# log

## 日誌的應用
- 日誌（log）在記錄程式運行時的資訊和錯誤中扮演關鍵角色。

## log套件的使用
- `log` 套件提供多種日誌記錄方法。
- 支持自定義日誌記錄方式，如文件輸出、格式化、錯誤等級。

### 示例：使用log套件
```go
package main

import (
	"log"
	"os"
)

func init() {
	file, err := os.OpenFile("example.log", os.O_CREATE|os.O_APPEND|os.O_WRONLY, 0666)
	if err != nil {
		log.Panic(err)
	}
	log.SetOutput(file)
}

func main() {
	log.Println("這是一條普通日誌")
	log.Fatalf("這是一條嚴重錯誤日誌，將會中斷程式")
}
```

## 日誌錯誤等級
- `log` 提供不同錯誤等級的方法，如 `Println`、`Fatal`、`Panic`。

## 自定義日誌配置
- 可以設置日誌的輸出目標、格式化方式和前綴等。
