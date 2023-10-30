---
title: "迴圈"
draft: false
slug: "class-10"
series: ["Go基礎入門"]
series_order: 10
date: "2089-07-07"
---
10: 本節課透過實際應用示例 Go 迴圈與遍歷運用。

# `for`

## 1. 概述
- Go 僅提供 `for` 作為循環控制結構，但支持三種不同的使用方式。

## 2. 無限循環
- 使用空 `for` 語句創建無限循環，類似於其他語言的 `loop`。
- 例子：
    ```go
    for {
        // 循環體
        if 條件 {
            break // 終止循環
        }
    }
    ```

## 3. 條件循環
- 在 `for` 後直接跟上條件表達式，當條件為 `true` 時執行循環。
- 例子：
    ```go
    i := 1
    for i < 11 {
        // 循環體
        i++
    }
    ```

## 4. 標準的 for 循環
- 包含初始化語句、條件表達式和後續操作。
- 例子：
    ```go
    for j := 1; j < 11; j++ {
        // 循環體
    }
    ```

## 5. 控制語句
- `break`：用於退出循環。
- `continue`：用於跳過當前循環迭代，繼續下一次迭代。

## 6. 應用實例：持續監控系統狀態
- 範例代碼：
    ```go
    package main

    import (
        "fmt"
        "time"
    )

    func main() {
        for {
            // 假設這是一個檢查系統健康狀態的函數
            err := checkSystemHealth()
            if err != nil {
                fmt.Println("警告：系統出現異常 -", err)
            } else {
                fmt.Println("系統運行正常")
            }

            // 每30秒檢查一次
            time.Sleep(30 * time.Second)
        }
    }

    func checkSystemHealth() error {
        // 這裡實現實際的健康檢查邏輯
        // 返回nil表示無錯誤，即系統運行正常
        // 返回非nil表示檢測到問題
        return nil
    }

    ```
在這個例子中，無限循環 for {} 持續運行，每30秒調用一次 checkSystemHealth() 函數來檢查系統的健康狀態。如果發現問題（即 checkSystemHealth() 返回錯誤），則輸出警告信息；如果一切正常，則輸出系統運行正常的信息。

這種模式常用於需要持續執行和監控的後台任務，例如服務器健康監控、定期數據同步等。通过 for {} 無限循環，可以確保程序持續執行，直到被手動中止或因錯誤而停止。

---

# `for range`

## 1. 基本概念
- `for range` 提供一種簡潔的方式來遍歷數據結構，如slices、arrays和maps。

## 2. 遍歷slices和arrays
- 在遍歷slices或arrays時，`for range` 返回兩個值：索引和該索引位置的元素值。
- 例子：
    ```go
    numbers := []int{1, 2, 3}
    for index, value := range numbers {
        fmt.Println(index, value)
    }
    ```

## 3. 遍歷maps
- 在遍歷maps時，`for range` 返回兩個值：鍵（key）和對應的值（value）。
- 例子：
    ```go
    capitals := map[string]string{"France": "Paris", "Italy": "Rome"}
    for country, capital := range capitals {
        fmt.Println(country, capital)
    }
    ```

## 4. 忽略索引或值
- 使用下劃線（`_`）忽略 `for range` 循環中的索引或值。
- 例子：
    - 只獲取值：`for _, value := range numbers {...}`
    - 只獲取索引：`for index := range numbers {...}`

## 5. 遍歷字符串
- 在遍歷字符串時，`for range` 返回字符的索引以及該索引處的字符（Unicode碼點）。
- 例子：
    ```go
    for index, runeValue := range "hello" {
        fmt.Printf("%d: %c\n", index, runeValue)
    }
    ```

## 6. 遍歷channel
- 使用 `for range` 可以持續從channel接收值，直到channel被關閉。
- 例子：
    ```go
    ch := make(chan int)
    // ... 在其他goroutine中向ch發送數據 ...
    for value := range ch {
        fmt.Println(value)
    }
    ```

---

# `label` 與 `goto`

## 1. `label` 和 `goto`
- `label` 是用於標記代碼塊的一種方式，通常與循環（如 `for`）和 `goto` 結合使用。
- `goto` 可用於跳轉到標記的 `label`。

## 2. 使用 `label` 和 `goto` 的實例
### 2.1. 結合 For 循環和 label
- 在 `for` 循環中，使用 `label` 跳出多層嵌套循環。
    ```go
    outside:
    for i := 0; i < 10; i++ {
        for j := 0; j < 10; j++ {
            for k := 0; j < 10; j++ {
                if i == 6 && j == 5 && k == 1{
                    break outside
                }
                // 循環內操作
            }
        }
    }
    ```

### 2.2. `goto` 的使用
- 使用 `goto` 進行條件跳轉。
- 注意：`goto` 雖然強大，但不建議頻繁使用，因為它可能會導致代碼結構混亂。
    ```go
    if someCondition {
        goto labelName
    }
    // 更多代碼
    labelName:
    // 跳轉目標
    ```

## 3. 注意事項
- 避免過度使用 `goto`，因為它可能會使代碼難以理解和維護。
- 在使用 `label` 和 `goto` 時，謹慎考慮代碼的可讀性和維護性。
