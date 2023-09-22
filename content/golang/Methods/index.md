---
title: "方法"
draft: false
slug: "class-12"
series: ["Go基礎入門"]
series_order: 12
---
本節課介紹 Go 方法的概念、宣告和使用方式。

## 方法的基本概念
- **定義**: 方法是綁定到特定類型的函式。
- **規則**: 方法必須與其所屬的類型定義在同一個套件中。

## 方法的宣告與使用
- **方法宣告**: 在函式名前加上一個接收者參數，指明該方法綁定的類型。
- **調用方法**: 使用類型實例加上點操作符來調用方法。
- **可見性**: 方法名首字母小寫為套件內私有(Private)，大寫為"匯出的"(Exported)，即公開、對外可見。  
- **自定義類型**:
    ```go
    type Employee struct {
        Name string
        ID   uint32
    }
    ```
- **方法宣告**:
    ```go
    func (e Employee) PrintName() {
        fmt.Println(e.Name)
    }
    ```


## 值接收子
值接收子(Value Receiver)
- **特點**: 方法接收值的副本，因此對該值的任何修改都不會影響原始實例。
- **使用時機**: 當您不需要修改原始實例時，或者當您需要保護原始數據不被意外修改時。
- **實際應用場景**: 假設我們有一個 `Account` 結構體，我們想要獲取賬戶的描述信息，但不修改任何資料。

  ```go
  type Account struct {
      HolderName string
      Balance    float64
  }

  func (a Account) Description() string {
      return fmt.Sprintf("Account Holder: %s, Balance: %.2f", a.HolderName, a.Balance)
  }
  ```

## 指標接收子
指標接收子(Pointer Receiver)
- **特點**: 方法接收指向值的指標，允許直接修改原始實例。
- **使用時機**: 當您需要修改原始數據，或者結構體過大而希望避免副本的開銷時。
- **實際應用場景**: 繼續使用前面的 `Account` 結構體，我們可能需要一個方法來更新賬戶的餘額。

  ```go
  func (a *Account) UpdateBalance(newBalance float64) {
      a.Balance = newBalance
  }
  ```
在這兩個例子中，`Description` 方法使用值接收子，它只讀取數據而不進行修改。而 `UpdateBalance` 方法使用指標接收子，允許它修改 `Account` 結構體的 `Balance` 字段。這種方法的選擇取決於您是否需要修改原始數據，以及是否關注性能（例如避免大型結構體的副本開銷）。

## 函式vs方法
何時該宣告函式？何時該使用方法？
- **使用函式**: 當操作不依賴於某個特定的類型時。
- **使用方法**: 當操作與某個特定的類型緊密相關時。
