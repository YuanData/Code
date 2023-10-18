---
title: "開發環境"
draft: false
slug: "class-01"
series: ["Go基礎入門"]
series_order: 1
date: "2098-07-07"
---
01：本節課說明 Go 的基本介紹和開發環境的設置流程，適用於入門學習者。

## Go 介紹
- **Go 概述**
  - 開源、靜態編譯型語言，由Google支持。
  - 有清晰的語法，適合初學者學習。
  - 強大的標準庫和優秀的並發性能。
  - 適合構建快速、可靠、高效的軟件。
  - 使用Garbage Collector實現內存安全。
  - 在速度上與Java和Kotlin相似，低於不使用Garbage Collector的語言如C++和Rust。

- **Go 的主要設計者**
  - Rob Pike：Unix先驅，參與了Plan 9和UTF-8的開發。
  - Ken Thompson：在貝爾實驗室工作，設計並實現了Unix操作系統。2006年，Thompson進入Google公司，與團隊共同設計了Go。

- **Go 的設計目標**
  - 2007年由Google設計，目標是利用高性能網絡和多核處理器。
  - 解決其他語言的問題，同時保留優點（如內存安全）。
  - 清晰、易學的語法，高效利用硬體。

- **Go 的使用現狀**
  - 專業開發者的十大主流語言之一，佔7%的市場份額。
  - 最受歡迎的開發領域是Web服務，佔36%。

## Go 開發環境搭建
- **安裝Go 的SDK**
  1. 訪問[go.dev](https://go.dev/)。
  2. 點擊“Download”下載最新穩定版。
  3. 根據操作系統和芯片類型選擇對應安裝包或壓縮包。

- **驗證安裝**
  - 在命令行輸入`go version`查看版本號。

- **設置GOPATH**
  - Windows：通過“此電腦”右鍵點屬性，進入高級系統設置修改環境變量。
  - Mac/Linux：使用`sudo nano`命令編輯配置文件，設置`GOPATH`。

- **開發工具**
  - VS Code、GoLand(付費)。
  - 安裝中文擴展，Code Runner和Go插件。
  - 將文件夾添加到VS Code工作區並保存。

- **驗證GOPATH設置**
  - 使用`go env`命令檢查GOPATH設定。
