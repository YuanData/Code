---
title: "實現JWT用戶認證"
draft: false
slug: "class-07"
series: ["軟體開發實務"]
series_order: 7
date: "2292-07-07"
---
07: 本節課介紹在 Go 利用 JWT 技術實現安全的用戶登入和驗證系統。

## JWT (JSON Web Tokens) 介紹

### JWT 基本概念
JWT (JSON Web Tokens) 是一種開放標準（RFC 7519），用於安全地在客戶端和伺服器之間傳輸資訊作為 JSON 物件。這種資訊可以被驗證和信任，因為它是數位簽名的。JWT 常用於身份驗證和資訊交換。
JWT 提供了無狀態的優勢，使得應用更容易擴展和跨平台使用，但它也需要嚴格的安全措施來保護簽名密鑰，並且需要策略來處理授權碼的失效問題。

### JWT 的結構
JWT 通常包含三個部分：
1. **標頭（Header）**: 通常包含兩部分資訊：授權碼的類型（通常是 JWT）和所使用的簽名算法（例如 HMAC SHA256 或 RSA）。
2. **負載（Payload）**: 包含聲明（Claims）。聲明是關於實體（通常是用戶）和其他數據的陳述。有三種類型的聲明：已註冊、公共和私有聲明。
3. **簽名（Signature）**: 用於驗證消息在傳輸過程中未被更改，並且，在知道簽名秘密的情況下，用於驗證JWT的發送者的身份。

### JWT 的使用流程
1. **用戶認證**: 用戶提供認證（如用戶名和密碼）給伺服器。
2. **生成 JWT**: 伺服器驗證認證，如果有效，則創建一個包含用戶資訊的 JWT，並將其發送回用戶。
3. **客戶端儲存 JWT**: 用戶端接收 JWT 並在後續的請求中將其作為憑證使用。
4. **伺服器驗證 JWT**: 每當用戶端發送請求時，都會附帶 JWT。伺服器將驗證 JWT 的簽名並提取用戶資訊以進行授權。

### JWT 的優點和限制
- **優點**: 
  - **無狀態和可擴展性**: 由於JWT包含所有必要的資訊，伺服器不需要在伺服器端儲存會話資訊，這使得JWT成為擴展應用程式的理想選擇。
  - **跨語言支援**: 作為一個基於JSON的標準，JWT可以被任何廣泛使用的程式語言處理。
- **限制**:
  - **安全性考量**: JWT的安全性取決於簽名秘密（對於對稱算法）或私鑰（對於非對稱算法）的保護。如果這些密鑰被洩露，JWT的安全性就會受到嚴重威脅。
  - **授權碼失效問題**: 標準的JWT不支援撤銷，一旦JWT被發出，就會在其到期之前保持有效，這在某些情況下可能導致問題。

## JWT創建與驗證

### 實作憑證產生器介面
首先，建立一個名為 `token` 的新套件，並在其中創建一個 `authzr.go` 檔案。此檔案中定義了一個憑證產生器介面 (token authzr interface)，用於管理憑證的創建和驗證。接下來，實作結構：`jwtAuthzr` 實現該介面。這樣做的目的是方便在不同類型的憑證產生器之間切換。

```go
type Authzr interface {
    CreateToken(membername string, duration time.Duration) (string, error)
    VerifyToken(token string) (*Payload, error)
}
```

### 憑證產生器介面的方法
憑證產生器介面套件含兩個方法：`CreateToken()` 和 `VerifyToken()`。`CreateToken()` 方法接受使用者名稱和有效期限作為輸入，返回簽章的授權碼字串或錯誤。`VerifyToken()` 方法則接受授權碼字串作為輸入，返回一個 Payload 物件或錯誤。

### 定義 Payload 結構
在 `payload.go` 檔案中定義 Payload 結構，套件含了憑證的有效負載數據。主要欄位套件括使用者名稱、創建時間和過期時間。此外，為了在授權碼洩露時能夠作廢特定授權碼，還應添加一個唯一識別每個授權碼的 ID 欄位。

```go
type Payload struct {
    ID          uuid.UUID `json:"id"`
    Membername  string    `json:"membername"`
    IssuedTime  time.Time `json:"issued_time"`
    ExpiredTime time.Time `json:"expired_time"`
}
```

### 實作JWT 
在 `jwt_authzr.go` 檔案中，實作 `JWTAuthzr` 結構。為了簽章授權碼，這裡使用對稱鍵算法。接著實作 `CreateToken` 和 `VerifyToken` 方法。在 `CreateToken` 方法中，首先創建一個新的授權碼有效負載，然後使用 `jwt-go` 套件來生成簽章的授權碼字串。在 `VerifyToken` 方法中，則是解析並驗證授權碼的有效性。

```go
type JWTAuthzr struct {
    secretKey string
}

func (authzr *JWTAuthzr) CreateToken(membername string, duration time.Duration) (string, error) {
    payload, err := NewPayload(membername, duration)
    if err != nil {
        return "", err
    }

    jwtToken := jwt.NewWithClaims(jwt.SigningMethodHS256, payload)
    return jwtToken.SignedString([]byte(authzr.secretKey))
}

func (authzr *JWTAuthzr) VerifyToken(token string) (*Payload, error) {
    // ...驗證邏輯...
}
```

## Go實現登入API

### 添加授權碼產生器到API伺服器
首先，在 `api/server.go` 文件中，於 `Server` 結構體中添加一個 `tokenAuthzr` 字段，類型為 `token.Authzr` 介面。然後，在 `NewServer` 函式中初始化此字段，創建一個新的授權碼產生器物件，選擇使用 JWT，並調用 `token.NewJWTAuthzr`，需要一個密

鑰字串，將從環境變數中加載。

```go
func NewServer(config util.Config, store db.Store) (*Server, error) {
    tokenAuthzr, err := token.NewJWTAuthzr(config.TokenSecretKey)
    if err != nil {
        return nil, fmt.Errorf("token authzr err: %w", err)
    }

    server := &Server{
        config:     config,
        store:      store,
        tokenAuthzr: tokenAuthzr,
    }

    server.setupRouter()
    return server, nil
}
```

### 更新環境變數和配置結構
在 `config.yaml` 文件中添加一個新的環境變數 `TOKEN_SECRET_KEY`，並確保此密鑰長度為 32 位元組，並添加另一個變數以儲存訪問授權碼的有效期限。再更新配置結構以套件含這兩個新變數，套件括對稱鍵和訪問授權碼持續時間。

```go
type Config struct {
    // ...其他配置...
    TokenSecretKey        string        `mapstructure:"TOKEN_SECRET_KEY"`
    AccessTokenDuration   time.Duration `mapstructure:"ACCESS_TOKEN_DURATION"`
}
```

### 實現用戶登入API
在 `member.go` 文件中，定義用戶登入請求 (`loginMemberRequest`) 和回應 (`loginMemberResponse`) 結構。`loginMemberResponse` 結構中最重要的欄位是返回給客戶端的 `AccessToken` 字串。接著，實現 `loginMember` 方法，該方法接受 `gin.Context` 對象作為輸入，並綁定 API 輸入參數至 `loginMemberRequest` 物件。接下來，從資料庫中檢索用戶並驗證密碼。如果驗證成功，則使用授權碼產生器創建新的訪問授權碼。

```go
func (server *Server) loginMember(ctx *gin.Context) {
    var req loginMemberRequest
    if err := ctx.ShouldBindJSON(&req); err != nil {
        ctx.JSON(http.StatusBadRequest, errorResponse(err))
        return
    }

    member, err := server.store.GetMember(ctx, req.Membername)
    if err != nil {
        if err == sql.ErrNoRows {
            ctx.JSON(http.StatusNotFound, errorResponse(err))
            return
        }
        ctx.JSON(http.StatusInternalServerError, errorResponse(err))
        return
    }

    err = util.VerifyPassword(req.Password, member.PasswordHash)
    if err != nil {
        ctx.JSON(http.StatusUnauthorized, errorResponse(err))
        return
    }

    accessToken, err := server.tokenAuthzr.CreateToken(
        member.Membername,
        server.config.AccessTokenDuration,
    )
    if err != nil {
        ctx.JSON(http.StatusInternalServerError, errorResponse(err))
        return
    }

    rsp := loginMemberResponse{
        AccessToken: accessToken,
        Member:        newMemberResponse(member),
    }
    ctx.JSON(http.StatusOK, rsp)
}
```

### 處理API端點路由和錯誤
將登入請求的路由添加到伺服器中，並處理可能發生的錯誤情況，如用戶名不存在、密碼不正確或其他內部伺服器錯誤。

```go
func (server *Server) setupRouter() {
    router := gin.Default()

    router.POST("/members", server.createMember)
    router.POST("/members/login", server.loginMember)

    // ...其他路由設定...

    server.router = router
}
```

### 測試和驗證API功能
透過單元測試確保新的伺服器結構正常工作，並通過實際發送請求到伺服器（例如使用 curl）來測試和驗證用戶登入API的功能。

```go
func newTestServer(t *testing.T, store db.Store) *Server {
	config := util.Config{
		TokenSecretKey:   util.RandomString(32),
		AccessTokenDuration: time.Minute,
	}

	server, err := NewServer(config, store)
	require.NoError(t, err)

	return server
}

type Server struct {
	config     util.Config
	store      db.Store
	tokenAuthzr token.Authzr
	router     *gin.Engine
}

func NewServer(config util.Config, store db.Store) (*Server, error) {
	tokenAuthzr, err := token.NewJWTAuthzr(config.TokenSecretKey)
	if err != nil {
		return nil, fmt.Errorf("token authzr err: %w", err)
	}

	server := &Server{
		config:     config,
		store:      store,
		tokenAuthzr: tokenAuthzr,
	}

	if v, ok := binding.Validator.Engine().(*validator.Validate); ok {
		v.RegisterValidation("symbol", validSymbol)
	}

	server.setupRouter()
	return server, nil
}

func TestLoginMemberAPI(t *testing.T) {
    // ... (其他代碼)
    server := newTestServer(t, store)
    recorder := httptest.NewRecorder()

    url := "/members/login"
    request, err := http.NewRequest(http.MethodPost, url, bytes.NewReader(data))
    require.NoError(t, err)

    server.router.ServeHTTP(recorder, request)
    tc.checkResponse(recorder)
}
```
## 小結
本節課講解在 Go 中實現了一個用戶登入API，該API能夠驗證用戶憑證並根據驗證結果返回 JWT 訪問授權碼。通過詳細介紹如何集成授權碼產生器、處理登入邏輯和錯誤，以及在不同授權碼類型之間進行切換，為開發安全、有效的後端登入系統提供指南。

---
## 課程檔案記錄
{{< ghdiff "d32d017d18321f8f0a859438d22302bbf540f90c" "L" "go.sum" >}}