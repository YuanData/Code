---
title: "實作middleware"
draft: false
slug: "class-08"
series: ["軟體開發實務"]
series_order: 8
date: "2291-07-07"
---
08: 本節課程講解如何實作身份驗證與中介軟體授權邏輯，並通過單元測試確保其正確性。

## 實作Gin框架下的身份驗證與授權中介軟體
在先前的課程中，已經實現了登入API，用於驗證會員並返回存取token給客戶端。然而，目前所有API均未實施任何形式的身份驗證和授權。這意味著，即使未經授權，任何人都能夠查看系統中的所有交易者資訊。為了解決這個問題，本課程將引入基於Bearer token的授權層。當會員透過請求的Authorization標頭提供存取token時，伺服器將驗證此token，僅返回對應會員所擁有的交易者列表。

```go
const (
    authztnHeaderKey  = "authorization"
    authztnTypeBearer = "bearer"
    authztnPayloadKey = "auth_payload"
)

func authztnMiddleware(tokenAuthzr token.Authzr) gin.HandlerFunc {
    // ...中間件邏輯...
}
```

## 中介軟體的工作原理
中介軟體在處理請求時，先進行日誌記錄或身份驗證等前置處理。中介軟體與處理函數的主要區別在於，中介軟體可以中斷請求並直接向客戶端發送回應。例如，在身份驗證中介軟體中，會解析和檢查存取token是否有效。如果token無效或過期，則呼叫ctx.Abort()停止請求並向客戶端發送401未授權狀態碼。如果token有效，則將解析的token有效負載儲存在上下文中，然後呼叫ctx.Next()轉發給實際的處理函數。

## 實作身份驗證中介軟體
首先在api包中創建middleware.go檔案，並宣告一個authztnMiddleware函數，接收一個tokenMaker介面作為輸入參數，返回一個gin.HandlerFunction作為輸出。這個函數不是中介軟體本身，而是一個高階函數，用於返回身份驗證中介軟體函數。此中介軟體函數的實作涉及從請求中提取授權標頭，檢查存取token的有效性，並將有效負載儲存到上下文中，以供後續處理函數使用。

```go
func authztnMiddleware(tokenAuthzr token.Authzr) gin.HandlerFunc {
    // ...中間件邏輯...
}
```

## 單元測試與中介軟體整合
為確保中介軟體的正確性，進行了單元測試。測試包括檢查當授權標頭缺失、格式不正確、或token過期時，中介軟體應正確拒絕請求。確認中介軟體工作正常後，將其整合到伺服器設定中，並應用於除了創建會員和會員登入API外的所有API路由。

```go
func TestAuthztnMiddleware(t *testing.T) {
    // ...單元測試邏輯...
}
```

```go
authztnRoutes := router.Group("/").Use(authztnMiddleware(server.tokenAuthzr))
authztnRoutes.POST("/traders", server.createTrader)
authztnRoutes.GET("/traders/:id", server.getTrader)
authztnRoutes.GET("/traders", server.listTraders)
authztnRoutes.POST("/records", server.createRecord)
```

## 實作授權邏輯
授權規則通常是API特定的，需在每個處理函數中實作。例如，在創建交易者API中，應確保會員僅能為自己創建交易者。這要求從授權有效負載中獲取會員名稱，並將其設為交易者的持有者。對於其他API，如獲取和列出交易者，同樣需要添加授權檢查，確保會員只能訪問屬於他們的資訊。

```go
func (server *Server) createTrader(ctx *gin.Context) {
    authPayload := ctx.MustGet(authztnPayloadKey).(*token.Payload)
    arg := db.CreateTraderParams{
        Holder:  authPayload.Membername,
        Symbol:  req.Symbol,
        Rest:    0,
    }
    // ...創建交易者邏輯...
}

func (server *Server) getTrader(ctx *gin.Context) {
    authPayload := ctx.MustGet(authztnPayloadKey).(*token.Payload)
    if trader.Holder != authPayload.Membername {
        err := errors.New("trader not under member")
        ctx.JSON(http.StatusUnauthorized, errorResponse(err))
        return
    }
    // ...獲取交易者邏輯...
}

func (server *Server) listTraders(ctx *gin.Context) {
    authPayload := ctx.MustGet(authztnPayloadKey).(*token.Payload)
    arg := db.ListTradersParams{
        Holder:  authPayload.Membername,
        Limit:   req.PageLmt,
        Offset:  (req.PageNum - 1) * req.PageLmt,
    }
    // ...列出交易者邏輯...
}
```

## 小結
本節課程講解在Gin框架中，如何實作身份驗證與授權中介軟體，並通過單元測試確保其正確性。授權邏輯被整合到每個API處理函數中，以保證數據安全性和正確的訪問控制。透過這一系列實現，API的安全性得到有效加強，確保只有合適的使用者能夠存取和操作他們擁有的資源。

---
## 課程檔案記錄
{{< ghdiff "3c72d815f9b9d1e3a4a9c854764e84340862b96d" "L">}}