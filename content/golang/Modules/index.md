---
title: "模組"
draft: false
slug: "class-02"
series: ["Go基礎入門"]
series_order: 2
date: "2097-07-07"
---
02：本節課涵蓋了 Go 模組管理的基本操作，包括模組初始化，以及項目結構的標準化。

## Go 模組管理基礎
- **創建一個基本的Go程序**
  1. 在VS Code中打開工作區。
  2. 創建一個新的Go文件，例如`helloworld.go`。
  3. 定義套件名，例如`package main`。
  4. 定義主函數`func main()`。
  5. 使用`fmt.Println("Let's Go")`進行輸出。

- **運行Go程序**
  1. 在VS Code中使用終端或命令行。
  2. 編譯程序：`go build helloworld.go`。
  3. 執行編譯後的可執行文件，或使用`go run helloworld.go`直接運行。

- **Go Modules初始化**
  1. 在終端中定位到項目的根目錄。
  2. 使用`go mod init 項目名`初始化模組（例如`go mod init goproj`）。
  3. 生成的`go.mod`文件涵蓋項目名、Go版本和依賴套件信息。

## Go Modules進階操作
- **在不同位置調用函數**
  1. 在同一套件的不同文件中調用函數。
  2. 使用`package main`定義不同文件中的函數。
  3. 使用`go run`運行整個套件，而非單個文件。

- **跨套件調用函數**
  1. 創建新的套件和文件，例如`tool.go`在`tool`套件中。
  2. 將函數移至新套件，並使用大寫字母開頭使其可被其他套件調用。
  3. 在其他套件中使用`import`引入新套件，並調用函數。

- **項目結構與標準**
  1. 對於大型項目，應遵循Go的項目結構標準。
  2. 常見結構包括：API、Assets、Build、Cmd等目錄。
  3. 小型項目可以簡化結構，直接在頂層目錄中使用`main.go`。

- **在CMD子套件中運行程序**
  1. 創建`cmd`目錄，用於存放不同的子項目，如服務器端和客戶端。
  2. 在`cmd`目錄下創建子項目，每個涵蓋一個`main.go`文件。
  3. 使用`go run`或`go build`運行或編譯這些子項目。
