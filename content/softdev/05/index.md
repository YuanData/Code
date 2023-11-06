---
title: "用 Mock優化 API測試"
draft: false
slug: "class-05"
series: ["軟體開發實務"]
series_order: 5
date: "2294-07-07"
---
05: 本節課介紹使用mock生成模擬介面進行HTTP API單元測試。

## 模擬資料庫介紹
在實作RESTful HTTP API的過程中，測試API時選擇連接真實資料庫或模擬資料庫是一個重要決策。模擬資料庫（Mocking Database）有其顯著優勢：首先，它支持獨立測試，每個測試都使用自己的模擬資料庫，避免了資料衝突。其次，測試執行速度更快，因為無需等待資料庫查詢的執行。最後，模擬資料庫可以輕鬆設定和測試邊緣案例，如意外錯誤或連接中斷，這在使用真實資料庫時往往難以實現。

## 實現API的Mock測試
有兩種主要的資料庫模擬方法。第一種是實現一個假的資料庫，它在記憶體中存儲資料。例如，通過Store介面定義的一系列操作，然後使用MemStore結構體來實現這些操作，但只使用映射來讀寫資料。這種方法雖然簡單易行，但需要編寫大量僅用於測試的程式，這在後續的開發和維護中可能相當耗時。

第二種更有效的方法是使用Stub代替假資料庫。這裡使用gomock套件生成並建立Stub，返回硬編碼的值以應對不同測試情境。例如，gomock會生成一個MockStore，然後通過其EXPECT()函式建立Stub，設定特定函式在給定輸入時的調用次數和返回值。

```go
// Makefile 更新，添加模擬資料庫生成命令
+mock:
+	mockgen -package mockdb -destination db/mock/store.go github.com/YuanData/allegro-trade/db/sqlc Store
```

## 設置和使用gomock
首先，安裝gomock，並在終端運行`go get`指令以安裝該套件。接著，確保生成的mockgen二進制文件在任何位置都可執行。如果遇到路徑問題，需要修改.zshrc或.bash_profile文件以套件含go/bin文件夾。

為了使用mockgen生成mock資料庫，需要對現有程式進行少量修改。將NewServer函式接受的db store對象替換為介面。將Store結構體改為Store介面，並相應更新NewStore()函式和其他相關函式。最後，使用sqlc套件提供的功能生成套件含所有所需函式的Querier介面，並將其嵌入Store介面中。

```go
// 更新 Server 結構體，將 store 定義為介面而非具體類型
-func NewServer(store *db.Store) *Server {
+func NewServer(store db.Store) *Server {
 	server := &Server{store: store}
 	router := gin.Default()
 
 // 更新 Store 介面定義，包含了所有需要的數據庫操作方法
+type Store interface {
+	Querier
+	RecordTx(ctx context.Context, arg RecordTxParams) (RecordTxResult

, error)
+}
```

## 創建和設置測試檔案
在進行HTTP API測試時，首先在api套件中創建一個新的文件`trader_test.go`。本課將專注於寫Get Trader API的測試。在`trader_test.go`文件中，定義了`TestGetTraderAPI`函式，用於測試API。首先實現一個生成隨機交易者的函式`randomTrader()`，該函式返回一個具有隨機ID、持有者、餘額的`db.Trader`對象。

```go
// 測試 GetTraderAPI 的函式
func TestGetTraderAPI(t *testing.T) {
    // ...省略測試案例的具體定義和執行...
}
```

## 構建模擬存儲與Stub
使用gomock生成的函式創建新的模擬存儲，並傳入由`gomock.NewController`創建的控制器對象。接著，建立Get Trader方法的Stub，這個方法是GetTraderAPI處理程序唯一會調用的方法。在Stub中設定預期的輸入參數和返回值，以及調用次數。

```go
// 構建模擬存儲與Stub
ctrl := gomock.NewController(t)
defer ctrl.Finish()

store := mockdb.NewMockStore(ctrl)
tc.buildStubs(store)
```

## 測試HTTP服務器和回應
創建一個使用模擬存儲的測試HTTP服務器，並使用`httptest`套件的ResponseRecorder來記錄API請求的回應。檢查HTTP狀態碼是否為`http.StatusOK`，並運行測試以確保其通過。

```go
// 測試HTTP服務器和回應
server := NewServer(store)
recorder := httptest.NewRecorder()

url := fmt.Sprintf("/traders/%d", tc.traderID)
request, err := http.NewRequest(http.MethodGet, url, nil)
require.NoError(t, err)

server.router.ServeHTTP(recorder, request)
tc.checkResponse(t, recorder)
```

## 擴展測試
為了讓測試更加完整，應該檢查回應主體是否與預期的交易者對象匹配。實現一個`requireBodyMatchTrader`函式來進行此檢查，並在測試中調用該函式。

```go
// 擴展測試，檢查回應主體
func verifyResponseBufferTrader(t *testing.T, body *bytes.Buffer, trader db.Trader) {
    data, err := ioutil.ReadAll(body)
    require.NoError(t, err)

    var gotTrader db.Trader
    err = json.Unmarshal(data, &gotTrader)
    require.NoError(t, err)
    require.Equal(t, trader, gotTrader)
}
```

## 測試資料集
為了涵蓋Get Trader API的可能場景，將單元測試轉換為測試集。定義一系列的測試案例，每個案例套件括一個唯一名稱、交易者ID、建立Stub的函式和檢查回應的函式。通過迭代測試案例並為每個案例運行子測試來執行測試。

```go
func TestGetTraderAPI(t *testing.T) {
    // ...
    testCases := []struct {
        name          string
        traderID      int64
        buildStubs    func(store *mockdb.MockStore)
        checkResponse func(t *testing.T, recorder *httptest.ResponseRecorder)
    }{
        // 測試案例定義
    }

    for i := range testCases {
        tc := testCases[i]
        t.Run(tc.name, func(t *testing.T) {
            // ...
        })
    }
}
```

## 提高覆蓋率
添加不同的測試案例，如交易者未找到、內部服務器錯誤和錯誤的請求，以確保對Get Trader處理程序的所有分支進行測試，從而提高程式覆蓋率。

```go
// 範例：涵蓋「交易者未找到」的測試案例
{
    name:      "Missing",
    traderID:  trader.ID,
    buildStubs: func(store *mockdb.MockStore) {
        store.EXPECT().
            GetTrader(gomock.Any(), gomock.Eq(trader.ID)).
            Times(1).
            Return(db.Trader{}, sql.ErrNoRows)
    },
    checkResponse: func(t *testing.T, recorder *httptest.ResponseRecorder) {
        require.Equal(t, http.StatusNotFound, recorder.Code)
    },
},
```

## 小結
本課程展示了如何使用gomock生成模擬介面，並利用它為Get Trader API撰寫單元測試，實現更高的程式覆蓋率。這些方法不僅提高了測試的速度和易用性，而且使測試更加穩健和安全。同樣的方法也可以應用於撰寫其他HTTP API的測試。

---
## 課程檔案記錄
{{< ghdiff "a0eb49dc83f4bbf0e23f5aad481a497ecc4db654" "L" "go.sum" >}}