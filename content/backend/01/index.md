---
title: "資料庫設計入門"
draft: false
slug: "class-01"
series: ["後端實務"]
series_order: 1
date: "2298-07-07"
---
本節課介紹資料庫模式設計、Docker容器實作 Postgres資料庫以及資料庫遷移。

## 資料庫模式設計
### 設計資料庫
資料庫設計是建立有效且有組織的資料庫結構的關鍵步驟。這一階段涉及到定義表結構，並確保這些表適當地表示資料間的關係。
開始設計時，首先定義表和關聯。例如，創建一個「traders」表來儲存帳戶信息，包括帳戶ID、所有者、餘額等信息。為了確保資料完整性和準確性，可以設定主鍵、外鍵以及其他約束條件，如非空（NOT NULL）和唯一（UNIQUE）等。

### 資料類型和約束條件的選擇
選擇合適的資料類型對於確保資料準確性和效率至關重要。例如，對於帳戶ID，選擇自增型別如 PostgreSQL 中的 `bigserial`；對於帳戶餘額，考慮使用 `bigint` 或在處理非整數貨幣時使用 `decimal`。此外，包括時間戳記時，使用 `timestamptz` 可包含時區信息，更為準確。

### 資料表的關聯性
在資料庫中，不同表之間的關聯性是通過外鍵來實現的。例如，在「records」表中，用外鍵 `trader_id` 來參照「traders」表，表明一個帳戶可以有多個條目。同樣地，「details」表記錄帳戶間的轉賬，也需要適當地設定外鍵以表示這種關聯。

### 索引的添加和優化
為了提升查詢效率，為常用的查詢欄位添加索引是必要的。例如，在「traders」表中，可能需要按所有者名稱查詢，因此將「holder」欄位加入索引；在「records」表中，則可能需要按帳戶ID查詢所有條目，因此將「trader_id」加入索引。

---
## 設定PostgreSQL
### 用 Docker 啟動 PostgreSQL 容器
安裝 Docker 是啟動 PostgreSQL 容器的第一步。在 Mac 上，這個過程非常簡單，只需從 Docker 官方網站下載並安裝。安裝完成後，啟動 Docker Desktop，並檢查其狀態以確保它已準備就緒。

使用 Docker 從 Docker Hub 拉取 PostgreSQL 容器的官方鏡像。例如，可以使用較小的 Alpine 版本來節省空間。下載完成後，使用 `docker run` 命令來啟動一個 PostgreSQL 資料庫服務器容器，並設置所需的環境變量，如用戶名和密碼。

### 連接和操作 PostgreSQL
可以通過 Docker 命令行直接與 PostgreSQL 容器互動，例如使用 `docker exec` 來訪問 PostgreSQL 控制台。此外，可以查看容器的日誌來瞭解容器內部的情況。

### 創建和修改資料庫
運行 SQL 腳本來創建和修改資料庫模式。例如，可以添加或修改約束條件，如 NOT NULL 約束，以確保數據完整性。

---
## 資料庫遷移
### 安裝 Go Migrate
首先，在 Mac 上使用 Homebrew 安裝 Go Migrate。
這個庫支持多種資料庫引擎，如 PostgreSQL、MySQL、MongoDB 和 CockroachDB。
完成安裝後，可通過 `migrate -version` 檢查版本，並使用 `migrate -help` 查閱手冊。

### 創建遷移文件
使用 `migrate create` 命令創建新的遷移文件。這將生成兩個文件，一個用於「升版」（up）遷移，另一個用於「降版」（down）遷移。將先前講座中生成的 SQL 內容複製到升版遷移文件中，在降版遷移文件中撰寫用於撤銷升版遷移更改的 SQL 腳本。

### Docker 命令和資料庫操作
使用 Docker 命令來管理 PostgreSQL 容器，包括啟動、停止、刪除容器，以及創建和刪除資料庫。

### 使用 Makefile 簡化流程
創建 Makefile 以簡化資料庫和容器的管理過程，包括創建和刪除資料庫、啟動和停止容器等。

### 執行資料庫遷移
使用 `migrate` 命令連接到資料庫並運行遷移腳本。升版遷移將應用所有未執行的遷移，而降版遷移則撤銷它們。

---
## 課程檔案記錄
{{< ghdiff "c53f4543b2dcbfc5c476df2e6a2b53f016a54228" "L" ".gitignore" >}}