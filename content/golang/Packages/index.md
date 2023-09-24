---
title: "套件"
draft: false
slug: "class-03"
series: ["Go基礎入門"]
series_order: 3
---
本節課說明 Go 的套件（Package）管理基礎以及init函式的特性。

## 套件（Package）的基本概念
- **套件的作用**：Go 程式由套件組成，主程式從 `main` 套件開始運行。
- **套件的引入**：使用 `import` 語句引入套件。
- **套件重名處理**：可以為引入的套件指定別名以解決重名問題。

### 套件引入示例
```go
import (
    "fmt"
    customName "myapp/util" // 為套件指定別名
)
```

## Init 函式
- **自動調用**：`init` 函式會在套件初始化時自動被調用，且在 `main` 函式之前執行。
- **每個套件的多個 Init 函式**：每個套件可以有多個 `init` 函式，並且都會被執行。

### Init 函式示例
```go
package mypackage

var globalVar int

func init() {
    globalVar = 10
    fmt.Println("init function in mypackage")
}

func MyFunction() {
    fmt.Println("Global variable:", globalVar)
}
```

## 套件的初始化順序
- **依賴套件的初始化**：首先初始化被依賴的套件的全域變數，然後運行它們的 `init` 函式。
- **主套件（Main Package）的初始化**：之後初始化 `main` 套件的全域變數與 `init` 函式。

### 全域變數與 Init 函式的初始化示例
```go
package main

import "mypackage"

func init() {
    fmt.Println("init function in main package")
}

func main() {
    mypackage.MyFunction()
}
```
