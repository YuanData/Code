---
title: "實現 CRUD 操作"
draft: false
slug: "class-02"
series: ["後端實務"]
series_order: 2
date: "2297-07-07"
---
本節課介紹如何使用 Go 撰寫並實現對資料庫的 CRUD（創建、讀取、更新、刪除）操作。

## sqlc介紹
sqlc 允許僅需編寫 SQL 查詢，便可自動生成 Go CRUD 程式，結合了 `database/sql` 的高性能和易用性。優點是編碼簡單且快速，且能在程式生成階段擷取 SQL 查詢錯誤。

## 安裝sqlc
首先訪問 sqlc.dev 並通過 Homebrew 安裝 sqlc。使用 `sqlc init` 生成設定文件，然後在 `sqlc.yaml` 中配置生成 Go 套件的名稱、路徑、查詢文件路徑等。透過 sqlc，可以方便地生成 CRUD 操作的程式，例如創建、讀取、更新和刪除賬戶。

##  設置sqlc

1. **初始化 sqlc**：在專案資料夾中運行 `sqlc init`。這將生成一個 `sqlc.yaml` 配置文件，這個文件最初是空的。

2. **配置 sqlc**：回到 sqlc 的 GitHub 頁面，選擇最新標籤的分支並查找設置選項。將這些設置複製並粘貼到 `sqlc.yaml` 文件中。

3. **設置 Go 套件名稱**：在 `sqlc.yaml` 文件中，使用 `name` 選項設置生成的 Go 套件名稱，例如 "db"。

4. **指定生成程式的路徑**：創建一個新的 "sqlc" 文件夾在 "db" 文件夾內，並將 `sqlc.yaml` 文件中的路徑設置為 `./db/sqlc`。

5. **設置查詢和模式路徑**：創建一個新的 "query" 文件夾在 "db" 文件夾內並設置查詢路徑為 `./db/query`。同樣地，將模式路徑設置為 `./db/migration`。

6. **選擇資料庫引擎**：在 `sqlc.yaml` 文件中，設置 `engine` 選項為 "postgresql" 或 "mysql"，具體取決於你的專案需求。

7. **其他設置**：設置 `emit_json_tags` 為 "true" 以在生成的結構中添加 JSON 標籤，設置 `emit_prepared_queries` 和 `emit_interface` 為 "false" 以簡化流程。

8. **生成 Go 程式**：在終端機運行 `make sqlc` 以生成程式。如果 `query` 文件夾中沒有查詢，會出現錯誤。

9. **撰寫 SQL 查詢**：在 `query` 文件夾中創建一個新的 `trader.sql` 文件，從 sqlc 的 GitHub 頁面複製指令的範例並將其修改為適合專案的查詢。

10. **生成模型程式**：再次運行 `make sqlc`，在 `db/sqlc` 文件夾中應該會看到新生成的文件，如 `models.go`，其中套件含 Trader、Record 和 Detail 的模型結構。

## 生成 Go 程式

1. **初始化和設置 sqlc**：在專案資料夾中運行 `sqlc init` 並配置 `sqlc.yaml` 文件。

2. **生成模型和介面程式**：
   - 生成的 `models.go` 文件套件含 Trader、Record 和 Detail 等模型的結構定義。
   - `db.go` 文件套件含 `dbtx` 介面，定義了 `sql.DB` 和 `sql.Tx` 對象共有的方法。

3. **撰寫 SQL 查詢並生成 CRUD 函式**：
   - 在 `trader.sql` 文件中撰寫 SQL 查詢，例如創建、讀取（Get 和 List）、更新和刪除帳戶的查詢。
   - 使用 sqlc 注釋來指導生成的 Go 函式簽章。

4. **運行 sqlc 生成程式**：
   - 使用 `make sqlc` 指令生成程式。
   - sqlc 會檢查 SQL 語法，並在生成程式之前報告任何錯誤。

5. **查看和理解生成的程式**：
   - `trader.sql.go` 文件中套件含由 SQL 查詢生成的 CRUD 函式。
   - 函式如 `CreateTrader`, `GetTrader`, `ListTraders`, `UpdateTrader`, 和 `DeleteTrader` 被自動生成。

6. **重要提示**：不要直接修改生成的程式，因為這些程式將在每次運行 `make sqlc` 時被重新生成。
---
## 課程檔案記錄
{{< ghdiff "832ff706a523a72a04ce2f3f0cb0ed427878094e" "L" "go.mod,db/migration/01_init_tables.up.sql" >}}
