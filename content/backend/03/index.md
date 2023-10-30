---
title: "建立單元測試"
draft: false
slug: "class-03"
series: ["後端實務"]
series_order: 3
date: "2296-07-07"
---
03: 本節課講解如何為資料庫CRUD操作撰寫單元測試，確保資料庫交互程式的正確性和穩定性。

## 單元測試的建立與結構
在Go中，單元測試的檔案應置於與程式相同的資料夾中，且檔案名稱以「_test.go」結尾。例如，針對「CreateTrader」功能，建立「trader_test.go」檔案。測試檔案的套件名稱應與原程式相同。每個單元測試函式都應以「Test」為前綴（首字母大寫），並接受一個「testing.T」物件作纺參數，用於管理測試狀態。

## 測試環境的配置
為了進行測試，首先需要設置資料庫連接和查詢物件。「TestMain」函式是單元測試的主入口，負責初始化測試環境。透過「sql.Open()」函式建立資料庫連接，使用Postgres驅動和資料庫源字符串。若連接成功，則創建全域的「testQueries」物件，用於後續的測試。

```go
package db

import (
    "database/sql"
    "log"
    "os"
    "testing"

    _ "github.com/lib/pq"
)

var testQueries *Queries

func TestMain(m *testing.M) {
    conn, err := sql.Open(dbDriver, dbSource)
    if err != nil {
        log.Fatal("sql open err: ", err)
    }
    testQueries = New(conn)
    os.Exit(m.Run())
}
```

## 引入外部套件
由於「database/sql」套件只提供與SQL資料庫交互的通用介面，需要結合特定的資料庫驅動。此例中使用Postgres，因此引入「lib/pq」驅動。在程式中引入該驅動時，使用空白標識符（下劃線）來避免Go格式化工具自動刪除未直接使用的導入。

## 單元測試的編寫
撰寫「CreateTrader」函式的單元測試。首先，定義「CreateTraderParams」，設置必要的參數，如帳戶名稱、餘額。調用「testQueries.CreateTrader()」執行測試，並檢查返回的帳戶對象和錯誤。使用「testify」套件的「require」子套件，進行錯誤檢查和結果驗證，以確保返回的帳戶資料符合預期。

```go
func TestCreateTrader(t *testing.T) {
    createRandomTrader(t)
}
```

## 使用隨機數據生成單元測試
為了提高單元測試的效率和有效性，建議採用隨機數據生成的方法。這不僅能節省確定測試值的時間，還能避免多個測試之間的衝突，特別是在資料庫中具有唯一性約束的欄位上。首先，在「util」資料夾中創建「random.go」文件，並在其中實現隨機數據生成的各種功能。

```go
package util

import "math/rand"

func init() {
    rand.Seed(time.Now().UnixNano())
}

func RandomInt(min, max int64) int64 {
    return min + rand.Int63n(max-min+1)
}
```

## 單元測試的改進和優化
通過將創建帳戶時的參數（例如帳號名稱、餘額）替換為隨機生成的數據，單元測試更加自動化和多樣化。此外，通過在Makefile中添加新的測試指令，可以方便地在終端機中執行單元測試並查看程式覆蓋率。

```shell
test:
    go test -v -cover ./...
```

## CRUD操作的單元測試
為了保持單元測試的獨立性和維護性，每個測試應該創建自己的帳戶記錄。分別為「GetTrader」、「UpdateTrader」、「DeleteTrader」和「ListTrader」等CRUD操作編寫單元測試，確保每個操作都能按預期執行並返回正確的結果。

```go
func TestGetTrader(t *testing.T) {
    // ... (參閱課程檔案記錄)
}

func TestUpdateTrader(t *testing.T) {
    // ... (參閱課程檔案記錄)
}

func TestDeleteTrader(t *testing.T) {
    // ... (參閱課程檔案記錄)
}

func TestListTraders(t *testing.T) {
    // ... (參閱課程檔案記錄)
}
```

## 小結
本節課講解了如何使用Go為資料庫CRUD操作撰寫單元測試的進階技巧，套件括使用隨機數據生成函式來提高測試的多樣性和獨立性，並詳細介紹了如何針對不同的CRUD操作編寫有效的單元測試。

---
## 課程檔案記錄
{{< ghdiff "646b0e77854ea74495ded0cf5697e7b3d567e10e" "L" >}}
