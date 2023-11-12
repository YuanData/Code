---
title: "用雜湊存儲密碼"
draft: false
slug: "class-06"
series: ["軟體開發實務"]
series_order: 6
date: "2293-07-07"
---
06: 本節課講解使用 bcrypt 儲存和驗證密碼，實現安全的會員創建API。

## 安全儲存會員密碼
在現代的後端開發中，保護會員的密碼安全是一個重要課題。這通常通過對密碼進行雜湊處理來實現，而不是將明文密碼直接存儲在資料庫中。bcrypt 雜湊函式是一個常用的選擇，因為它可以有效防止彩虹表攻擊。

### 密碼雜湊過程
- **雜湊函式選擇**: bcrypt 雜湊函式被廣泛使用，因其安全性高且可靠。
- **成本參數**: bcrypt 要求指定一個成本參數，決定算法的迭代次數。
- **隨機鹽值**: 為了增強安全性，bcrypt 在雜湊過程中會生成一個隨機鹽值。
- **雜湊值生成**: 最終生成的雜湊值套件含算法標識符、成本、鹽值和雜湊值本身。

## 實現密碼雜湊與驗證
用 bcrypt 雜湊函式實現密碼雜湊與驗證：

### 雜湊密碼
- **HashPassword 函式**: 接收明文密碼，返回 bcrypt 雜湊值。
- **bcrypt.GenerateFromPassword**: 用於生成密碼的 bcrypt 雜湊。
- **錯誤處理**: 確保在雜湊過程中處理任何可能的錯誤。

```go
func HashPassword(password string) (string, error) {
    hashedPassword, err := bcrypt.GenerateFromPassword([]byte(password), bcrypt.DefaultCost)
    if err != nil {
        return "", fmt.Errorf("failed to hash password: %w", err)
    }
    return string(hashedPassword), nil
}
```

### 驗證密碼
- **CheckPassword 函式**: 驗證提交的密碼是否與資料庫中的雜湊值匹配。
- **bcrypt.CompareHashAndPassword**: 用於比較明文密碼與雜湊值。

```go
func CheckPassword(password, hashedPassword string) error {
    return bcrypt.CompareHashAndPassword([]byte(hashedPassword), []byte(password))
}
```

## 實現會員建立 API
在開發過程中，將上述雜湊與驗證邏輯整合進會員建立 API 是必要的。

### 建立會員
- **接收會員資料**: 套件括會員名、密碼、全名和電子郵件。
- **雜湊處理密碼**: 在儲存到資料庫前，將會員密碼進行 bcrypt 雜湊處理。
- **錯誤處理**: 處理可能的錯誤，如會員名重複或資料庫連接問題。

```go
func (server *Server) createMember(ctx *gin.Context) {
    var req createMemberRequest
    if err := ctx.ShouldBindJSON(&req); err != nil {
        ctx.JSON(http.StatusBadRequest, errorResponse(err))
        return
    }

    hashedPassword, err := util.HashPassword(req.Password)
    if err != nil {
        ctx.JSON(http.StatusInternalServerError, errorResponse(err))
        return
   

 }

    arg := db.CreateMemberParams{
        Membername: req.Membername,
        PasswordHash: hashedPassword,
        NameEntire: req.NameEntire,
        Email: req.Email,
    }

    // ...剩餘的建立會員邏輯...
}
```

## 小結
本節課程深入探討了如何在 Go 中使用 bcrypt 來安全地雜湊和驗證會員密碼，並實現了一個安全的會員建立 API。透過這些技術，我們能夠有效保護會員資料的安全，並提高整體應用的安全性。

---
## 課程檔案記錄
{{< ghdiff "c561e374e1b07deb71a7224291daa3db800dba79" "L" "go.mod" >}}