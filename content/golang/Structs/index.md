---
title: "結構體"
draft: false
slug: "class-08"
series: ["Go基礎入門"]
series_order: 8
---
本節課介紹 Go 自定義數據類型與結構體的使用。並可模擬物件導向中的一些特性。

## 自定義數據類型
- **目的**: 提升代碼可讀性與類型安全性。
- **使用場景**: 分辨不同類型的資料，例如分辨不同類型的通訊訊息。
    ```go
    type MessageCode uint16
    type ErrorCode uint16
    ```

## 數據類型別名
- **概念**: 創建現有類型的新名稱，保持類型兼容性。
- **用途**: 簡化代碼，或者在特定上下文中重命名類型。
    ```go
    type RawCode = uint16
    ```

## 結構體
- **定義**: 組合多個不同類型的數據成為一個單一類型。
- **使用場景**: 表達複雜數據結構，如用戶資訊。

### 結構體的宣告與實例化
- **結構體宣告**:
    ```go
    type User struct {
        Name string
        ID   uint32
    }
    ```
- **實例化**:
    ```go
    var u1 User
    u1 = User{Name: "Alice", ID: 1000}
    ```

### 結構體指標
- **使用**: 透過指標操作結構體，可實現在函式間共享與修改結構體的數據。
    ```go
    var u2 *User
    u2 = &User{Name: "Bob", ID: 1001}
    ```

### 結構體模擬繼承
- **實現方式**: Go 中沒有傳統意義上的繼承，但可以通過嵌入其他結構體來模擬。
    ```go
    type Account struct {
        User
        Password string
    }
    ```

### 結構體標籤
- **用途**: Struct Tags常用於JSON序列化時字段名稱的轉換。
    ```go
    type User struct {
        Name string `json:"name"`
        ID   uint32 `json:"id"`
    }
    ```
