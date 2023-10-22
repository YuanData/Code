---
title: "開發環境設置"
draft: false
slug: "class-01"
series: ["後端實務"]
series_order: 1
date: "2298-07-07"
---
本節課介紹如何安裝 Docker以及如何連接和操作 Postgres 容器。

## 安裝和使用 Docker
安裝 Docker Desktop 是啟動 PostgreSQL 容器的第一步。在 Mac 上，這個過程非常簡單，只需從 Docker 官方網站下載並安裝。安裝完成後，啟動 Docker Desktop，並檢查其狀態以確保它已準備就緒。

## 下載和啟動 PostgreSQL 容器
使用 Docker 從 Docker Hub 拉取 PostgreSQL 容器的官方鏡像。例如，可以使用較小的 Alpine 版本來節省空間。下載完成後，使用 `docker run` 命令來啟動一個 PostgreSQL 資料庫服務器容器，並設置所需的環境變量，如用戶名和密碼。

## 連接和操作 PostgreSQL
可以通過 Docker 命令行直接與 PostgreSQL 容器互動，例如使用 `docker exec` 來訪問 PostgreSQL 控制台。此外，可以查看容器的日誌來瞭解容器內部的情況。

## 使用資料庫GUI工具
TablePlus 是一個 GUI 工具，可用於管理多種類型的資料庫。通過 TablePlus，可以輕鬆連接到 PostgreSQL 資料庫，執行 SQL 查詢，並檢視資料庫的結構和數據。此外，可以使用 TablePlus 運行之前設計的 SQL 腳本來創建和修改資料庫模式。

## 資料庫模式的創建和修改
在 TablePlus 中，可以運行 SQL 腳本來創建和修改資料庫模式。例如，可以添加或修改約束條件，如 NOT NULL 約束，以確保數據完整性。在執行更改後，可以在 TablePlus 中即時查看並確認這些更改。
