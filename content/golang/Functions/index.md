---
title: "函式"
draft: false
slug: "class-11"
series: ["Go基礎入門"]
series_order: 11
---
本節課說明 Go 函式、可變參數和匿名函式；與用defer延遲執行和recover錯誤處理。

# `func` 函式

## 1. 基礎函式定義與實作
- 定義一個計算平方的函式 `calculateSquare`。
```go
func calculateSquare(number int) int {
    return number * number
}
```

## 2. 實參和形參
- 調用 `calculateSquare` 函式時，傳入的參數是實參。
```go
square := calculateSquare(4) // 4 是實參
```

## 3. 返回值命名與使用
- 定義一個函式 `computeStats`，返回兩個整數的平均值與乘積。
```go
func computeStats(a, b int) (average, product int) {
    average = (a + b) / 2
    product = a * b
    return
}
```

## 4. 簡化參數類型聲明
- 定義一個函式 `addNumbers`，計算兩個整數的和。
```go
func addNumbers(a, b int) int {
    return a + b
}
```

## 5. 可變參數
- 定義一個函式 `calculateTotal`，計算多個整數的總和。
```go
func calculateTotal(nums ...int) int {
    total := 0
    for _, num := range nums {
        total += num
    }
    return total
}
```

## 6. 函式作為數據類型
- 將函式 `addNumbers` 賦值給變量 `add`。
```go
var add func(a int, b int) int = addNumbers
result := add(5, 7)
```

## 7. 匿名函式與立即調用
- 定義並立即調用一個匿名函式，計算兩數之和。
```go
sum := func(a, b int) int {
    return a + b
}(10, 15)
```
---

# `defer` 延遲執行

## `defer` 的基本概念
- **defer 定義**：`defer` 用於延遲執行函式內的語句，直到包含它的函式結束執行。

## 1. 使用 `defer` 的實際案例
- **案例一：追蹤函式調用順序**
```go
func trackFunctionOrder() func() {
    order := 0
    return func() {
        order++
        fmt.Printf("Function called in order: %d\n", order)
    }
}
```

## 2. `defer` 延遲執行的特性
- **案例二：defer 延遲執行**
```go
func deferExample() {
    deferCall := trackFunctionOrder()
    defer deferCall()
    fmt.Println("Function execution started")
}
```
- 執行 `deferExample` 會先輸出 "Function execution started"，然後在函式結束時執行 `deferCall`。

## 3. `defer` 延遲執行的堆疊行為
- **案例三：多個 defer 的執行順序**
```go
func multipleDefer() {
    defer fmt.Printf("deferred function %d\n", 1)
    defer fmt.Printf("deferred function %d\n", 2)
    defer fmt.Printf("deferred function %d\n", 3)
}
```
- 在這個例子中，`defer` 語句會按照它們被添加到呼叫堆疊的相反順序執行，這對於理解資源管理和錯誤恢復的流程非常重要。
- 當 `multipleDefer` 函式被呼叫時，輸出的順序將會是後進先出（LIFO）：
```shell
deferred function 3
deferred function 2
deferred function 1
```
## 4. 錯誤處理：`defer` 與 `recover` 結合使用
- **案例四：錯誤捕捉與處理**
```go
func errorHandlingExample() {
    defer func() {
        if err := recover(); err != nil {
            fmt.Println("recovered from error:", err)
        }
    }()

    fmt.Println(3 / 0) // 故意製造除以零錯誤
}
```
- 在 `errorHandlingExample` 函式中，`recover` 會捕捉到任何 panic，防止程式異常終止。
