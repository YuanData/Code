---
title: "互斥鎖"
draft: false
slug: "class-16"
series: ["Go基礎入門"]
series_order: 16
date: "2083-07-07"
---
16: 本節課說明 Go 的sync套件提供並發編程中的同步機制與資源管理工具。

## Mutex（互斥鎖）
- `sync.Mutex` 用於在多個Goroutine之間提供互斥訪問。
- 避免數據競爭和共享資源的衝突。

## WaitGroup
- `sync.WaitGroup` 用於等待一組Goroutine的完成。
- 通過計數器實現，`Add` 方法增加計數，`Done` 方法減少計數，`Wait` 方法阻塞直到計數器為零。

### 示例：WaitGroup的使用
```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	var wg sync.WaitGroup
	for i := 0; i < 5; i++ {
		wg.Add(1)
		go func(n int) {
			defer wg.Done()
			fmt.Printf("Goroutine %d 執行\n", n)
		}(i)
	}
	wg.Wait()
	fmt.Println("所有Goroutine執行完畢")
}
```

## Condition
- `sync.Cond` 提供對多個Goroutine的條件控制。
- 主要用於Goroutine之間的同步和事件通知。

### 示例：Condition的使用
```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	mutex := sync.Mutex{}
	cond := sync.NewCond(&mutex)
	
	for i := 0; i < 3; i++ {
		go func(id int) {
			cond.L.Lock()
			defer cond.L.Unlock()
			cond.Wait()
			fmt.Printf("Goroutine %d 被喚醒\n", id)
		}(i)
	}

	// 喚醒一個Goroutine
	cond.Signal()
	// 喚醒所有Goroutine
	cond.Broadcast()
}
```

## Once
- `sync.Once` 確保某個操作只執行一次。
- 主要用於初始化或單例模式。

### 示例：Once的使用
```go
package main

import (
	"fmt"
	"sync"
)

var once sync.Once

func setup() {
	fmt.Println("初始化操作")
}

func main() {
	for i := 0; i < 5; i++ {
		go func() {
			once.Do(setup)
		}()
	}
}
```

## Map
- `sync.Map` 提供並發安全的鍵值對存取。
- 支持無鎖設計，適合在多Goroutine環境下使用。

### 示例：Map的使用
```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	var sm sync.Map
	sm.Store("key1", "value1")
	sm.Store("key2", "value2")

	value, ok := sm.Load("key1")
	if ok {
		fmt.Println("key1:", value)
	}

	sm.Range(func(key, value interface{}) bool {
		fmt.Println(key, value)
		return true
	})
}
```

## Pool
- `sync.Pool` 提供物件池功能，用於存儲和複用臨時物件。
- 可以減少記憶體分配和減少GC壓力。

### 示例：Pool的使用
```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	pool := &sync.Pool{
		New: func() interface{} {
			return "新物件"
		},
	}

	obj := pool.Get()
	fmt.Println("1:", obj)

	pool.Put("已存在的物件")
	obj = pool.Get()
	fmt.Println("2:", obj)
}
```

## RWMutex（讀寫互斥鎖）
- `sync.RWMutex` 允許多個Goroutine同時讀取，但寫入時互斥。
- 提高讀取效能，適用於讀多寫少的場景。

### 示例：RWMutex的使用
```go
package main

import (
	"fmt"
	"sync"
	"time"
)

var (
	data    = make(map[int]int)
	rwMutex = sync.RWMutex{}
)

func readData(key int, wg *sync.WaitGroup

) {
	defer wg.Done()

	rwMutex.RLock()
	defer rwMutex.RUnlock()
	fmt.Printf("讀取: %d => %d\n", key, data[key])
}

func writeData(key, value int, wg *sync.WaitGroup) {
	defer wg.Done()

	rwMutex.Lock()
	defer rwMutex.Unlock()
	data[key] = value
	fmt.Printf("寫入: %d => %d\n", key, value)
}

func main() {
	var wg sync.WaitGroup

	// 寫操作
	wg.Add(1)
	go writeData(1, 100, &wg)

	// 讀操作
	for i := 0; i < 3; i++ {
		wg.Add(1)
		go readData(1, &wg)
		time.Sleep(100 * time.Millisecond)
	}

	wg.Wait()
}
```
