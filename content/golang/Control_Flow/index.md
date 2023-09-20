---
title: "控制流程"
draft: false
slug: "class-09"
series: ["Go基礎入門"]
series_order: 9
---
本節課是對 Go 控制流程的簡要介紹。

# `if...else` 結構

## 1. 基本用法
- 在 Go 中使用 `if` 和 `else` 來進行條件判斷。
- `if` 條件表達式不需要用括號包圍，但加上也不會報錯。
- 確保 `if` 條件和 `{` 在同一行，以避免自動加分號導致的錯誤。

## 2. 多重條件判斷
- 可以通過添加多個 `else if` 來擴展條件判斷，處理更多情況。

## 3. 注意事項
- 確保條件判斷的邏輯正確，以避免錯誤的條件分支。
- 在編寫複雜的條件判斷時，注意代碼的可讀性。

```go
package main

import (
    "fmt"
)

func main() {
    var year int
    fmt.Print("請輸入年份：")
    fmt.Scan(&year)

    // 首先檢查年份是否能被400整除
    if year%400 == 0 {
        fmt.Printf("%d 是閏年\n", year)
    } else if year%100 == 0 {
        // 然後檢查年份是否能被100整除
        fmt.Printf("%d 不是閏年\n", year)
    } else if year%4 == 0 {
        // 最後檢查年份是否能被4整除
        fmt.Printf("%d 是閏年\n", year)
    } else {
        // 如果以上條件都不滿足，則不是閏年
        fmt.Printf("%d 不是閏年\n", year)
    }
}
```
---

# `switch…case` 結構

## 1. 基礎概念
- `switch` 可以用於替換多個 `if...else` 分支。
- `case` 語句默認自帶 `break`，不需額外添加。
- 使用 `fallthrough` 可以實現穿透至下一個 `case`。

## 2. `switch` 條件語句
- 在 `switch` 中，可以直接放入原本在 `if` 後的條件。
- `default` 分支是可選的，當所有 `case` 條件都不滿足時執行。

## 3. 範例：替換多分支 `if...else`
```go
package main

import (
    "fmt"
)

func main() {
    var requestPath string
    fmt.Print("請輸入API請求路徑：")
    fmt.Scan(&requestPath)

    switch requestPath {
    case "/home":
        fmt.Println("執行首頁相關操作")
    case "/user":
        fmt.Println("執行用戶相關操作")
    case "/about":
        fmt.Println("執行關於頁面相關操作")
    case "/contact":
        fmt.Println("執行聯絡方式相關操作")
    default:
        fmt.Println("無效的請求路徑")
    }
}
```

## 4. 範例：使用 `fallthrough`
- `fallthrough` 使得程序繼續匹配並執行下一個 `case`。
```go
package main

import (
    "fmt"
)

func main() {
    var userRole string
    fmt.Print("請輸入用戶角色：")
    fmt.Scan(&userRole)

    switch userRole {
    case "admin":
        fmt.Println("擁有管理者權限")
        fallthrough
    case "editor":
        fmt.Println("擁有編輯和發佈內容的權限")
        fallthrough
    case "viewer":
        fmt.Println("擁有查看內容的權限")
    default:
        fmt.Println("未知的用戶角色")
    }
}
```
在這個例子中，我們根據用戶的角色（admin, editor, viewer）來賦予他們不同的權限。如果用戶是 admin，他們將擁有管理者權限，並通過 fallthrough 繼續獲得 editor 和 viewer 的權限。如果用戶是 editor，他們將擁有編輯和發佈權限以及查看權限。如果用戶是 viewer，他們只擁有查看內容的權限。使用 fallthrough 可以避免重複寫出共享權限的代碼，使得代碼更加簡潔和高效。

## 5. `if...else` 與 `switch` 的區別
- `if...else` 更適合用於單一或少量分支的條件判斷。
- `switch` 更適合用於多分支的條件判斷，使代碼更加清晰易讀。

## 6. 注意事項
- 確保 `switch` 每個 `case` 的條件不重疊，以避免邏輯錯誤。
- 使用 `fallthrough` 時要注意其邏輯順序和影響。
