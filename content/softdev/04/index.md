---
title: "實作 RESTful API"
draft: false
slug: "class-04"
series: ["軟體開發實務"]
series_order: 4
date: "2295-07-07"
---
04: 本節課講解使用Gin框架實現 RESTful HTTP API的完整流程，從框架設置到伺服器運行

## Go Web框架介紹
在Go中，有多個流行的網絡框架，例如Gin, Beego, Echo, Revel, Martini, Fiber, 和 Buffalo。這些框架提供了豐富的功能，如路由、參數綁定、驗證、中間件，甚至套件括內建的ORM。對於喜歡輕量級套件並僅需路由功能的開發者，Go還提供了多個流行的HTTP路由選擇，如Fast HTTP, Gorilla Mux, HTTP Router, 和 Chi。

## 安裝與設置Gin
本課程選用Gin框架進行教學。首先，需通過執行`go get`指令來安裝Gin套件。安裝後，在Go模組文件中會看到Gin作為新依賴項被添加。隨後，建立一個名為`api`的新文件夾，並在其中創建一個名為`server.go`的文件，用於實現HTTP API服務器。

```go
func NewServer(store *db.Store) *Server {
    server := &Server{store: store}
    router := gin.Default()

    router.POST("/traders", server.createTrader)
    server.router = router
    return server
}
```

## 構建HTTP API伺服器
定義一個名為`Server`的結構，它將處理所有針對交易服務的HTTP請求。這個結構套件含兩個字段：一個是之前講解中實現的`db.Store`，用於處理來自客戶端的API請求與資料庫的交互；另一個是`gin.Engine`類型的路由器，幫助將每個API請求正確地分派到對應的處理程序。

```go
type Server struct {
    store  *db.Store
    router *gin.Engine
}
```

## 設置路由與處理程序
在`Server`結構中添加一個`NewServer`函式，它接受一個`db.Store`作為輸入，並返回一個`Server`。這個函式會創建一個新的伺服器實例，並為該伺服器上的服務設置所有HTTP API路由。例如，添加第一個API路由以創建新交易者時，會使用POST方法，並調用相應的處理程序。

## 請求處理與資料驗證
在處理API請求時，首先定義一個用於存儲創建交易者請求的結構，並從HTTP請求的主體中獲取輸入參數。利用Gin內置的驗證器進行數據驗證，確保客戶端提供的數據是有效的。例如，可以使用`binding`標籤指定某些字段為必需。

```go
type createTraderRequest struct {
    Holder    string `json:"holder" binding:"required"`
    Symbol    string `json:"symbol" binding:"required,oneof=BTC ETH ADA"`
}

func (server *Server) createTrader(ctx *gin.Context) {
    var req createTraderRequest
    if err := ctx.ShouldBindJSON(&req); err != nil {
        ctx.JSON(http.StatusBadRequest, errorResponse(err))
        return
    }
    // ...
}
```

## 伺服器運行與配置
最後，添加一個`Start`函式到`Server`結構中，用於在指定地址上運行HTTP伺服器，開始監聽API請求。此外，在`main.go`文件中設置伺服器的入口點，套件括與資料庫的連接和伺服器的初始化。

```go
func main() {
    conn, err := sql.Open(dbDriver, dbSource)
    if err != nil {
        log.Fatal("sql open err: ", err)
    }

    store := db.NewStore(conn)
    server := api.NewServer(store)

    err = server.Start(serverAddress)
    if err != nil {
        log.Fatal("server start err", err)
    }
}
```

## 服務器啟動和API測試
為了方便啟動服務器，添加了一個新的Make命令`make server`，它執行`go run main.go`。透過Postman進行API測試，驗證創建交易者API的正常運作，包括成功創建交易者、處理無效數據的情況，並確認錯誤信息的準確性。

## 新增查詢API
實現了一個新的API以便通過ID獲取特定交易者的資料。這個API使用GET方法，並通過URI參數接收交易者ID。實現過程中，重點介紹了從URI中綁定和驗證參數的方法。

```go
type getTraderRequest struct {
    ID int64 `uri:"id" binding:"required,min=1"`
}

func (server *Server) getTrader(ctx *gin.Context) {
    var req getTraderRequest
    if err := ctx.ShouldBindUri(&req); err != nil {
        ctx.JSON(http.StatusBadRequest, errorResponse(err))
        return
    }
    // ...
}
```

## 分頁功能的實現
由於資料庫中的交易者數量可能非常大，因此引入了分頁機制。該機制允許客戶端每次API請求只獲取一頁數據。這部分主要介紹了從查詢字符串中獲取分頁參數（如頁碼和每頁限制數量）的方法，並說明如何基於這些參數從資料庫中查詢數據。

```go
type listTraderRequest struct {
    PageNum   int32 `form:"page_num" binding:"required,min=1"`
    PageLmt   int32 `form:"page_lmt" binding:"required,min=5,max=10"`
}

func (server *Server) listTrader(ctx *gin.Context) {
    var req listTraderRequest
    if err := ctx.ShouldBindQuery(&req); err != nil {
        ctx.JSON(http.StatusBadRequest, errorResponse(err))
        return
    }
    // ...
}
```

## 空集合處理
對於返回空集合的情況進行了優化，確保即使在沒有數據的情況下也能返回空列表，而不是null。這涉及了對sqlc工具的一些進階配置，以便生成更符合需求的代碼。

## 小結
本節課介紹如何使用Gin框架在Go中實現RESTful HTTP API，涵蓋了服務器啟動、API測試、特定資源的查詢、分頁處理以及空集合的優化處理。

---
## 課程檔案記錄
{{< ghdiff "6152d8a0e56e3a3812f39f6d217d2e00521d502f" "L" "go.sum" >}}
