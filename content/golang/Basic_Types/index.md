---
title: "基本資料型別"
draft: false
slug: "class-06"
series: ["Go基礎入門"]
series_order: 6
date: "2093-07-07"
---
06: 本節課是 Go 基本資料類型與指標概念的介紹。

# 基本資料型別

## 1. 值類型的變量和常量
- 在值類型的變量和常量在宣告時都會被分配內存空間並賦予默認值。
  ```go
  var intValue int       // 整數型默認值為 0
  var boolValue bool     // 布林型默認值為 false
  var stringValue string // 字符串默認值為空串 ""
  ```

## 2. 數據類型詳解
### 整數型
- Go中的 `int8` 用八個二進制位來存儲整數，其中一位用於表示正負號。
  - 正數範圍：0 到 127。
  - 負數範圍：-128 到 -1。
- `uint8`（或 `byte`）是無符號整型，只用於存儲正整數。
- `rune` 是 `int32` 的別名，常用於表示 Unicode 碼點。
  ```go
  var i8 int8  // int8 範圍: -128 到 127
  var u8 uint8 // uint8 (或 byte) 範圍: 0 到 255
  var runeVal rune = 'A' // rune 是 int32 的別名，用於表示 Unicode 碼點
  ```

### 浮點型
- `float32` 使用 32 位，`float64` 使用 64 位。
  ```go
  var f32 float32 // 32位浮點數
  var f64 float64 // 64位浮點數
  ```

## 3. 進制表示法
- 支持二進制（0b或0B開頭）、八進制（0o或0O開頭）、十進制和十六進制（0x或0X開頭）的表示法。
  ```go
  var bin = 0b1010    // 二進制
  var oct = 0o12      // 八進制
  var dec = 10        // 十進制
  var hex = 0x0A      // 十六進制
  ```

## 4. 數值類型轉換
- 數值類型之間可以相互轉換，但要注意數據範圍和精度問題。
  ```go
  var i int = 42
  var f float64 = float64(i)
  ```

## 5. 字符和字符串處理
- 字符的大小寫轉換可以通過 ASCII 碼值的運算實現。
- 字符串拼接可以用 `+` 運算符或者逗號（`,`）在 `Println` 函數中。
  ```go
  var char byte = 'A'
  var lowerChar = char + 32 // 轉為小寫
  var str1, str2 string = "Hello", "Go"
  var combined = str1 + ", " + str2 // 字符串拼接
  ```

## 6. 布林值
- 布林型（`bool`）只有 `true` 和 `false` 兩個值。
  ```go
  var trueVal bool = true
  var falseVal bool = false
  ```

## 7. 字符串長度
- 使用 `len` 函數來獲取字符串的長度，計算的是字節數。
  ```go
  var length = len("Hello, 世界") // 注意: 包含非ASCII字符時長度為字節數
  ```

---

# 指標

## 1. 值複製和值傳遞
- 值複製（Value Copy）和值傳遞（Value Passing）是理解指標的基礎。
- 值複製：函數調用時，參數值被複製一份，函數內的操作不影響原始值。
- 值傳遞：在函數調用過程中，傳遞的是數據的副本。
  ```go
  func modifyValue(val int) {
      val = 50 // 僅修改副本
  }
  var original = 42
  modifyValue(original)
  ```

## 2. 指標基本概念
- 指標是存儲另一個變量內存地址的變量。
- 使用 `&` 符號獲取變量的內存地址。
- 使用 `*` 符號訪問指標指向的內存地址的值。
  ```go
  var a int = 10
  var p *int = &a // 指向 a 的指標
  ```

## 3. 使用指標的目的
- 指標允許直接修改內存地址中存儲的數據，而非副本。
- 這使得函數能夠直接修改傳入參數的原始值。
  ```go
  func modifyThroughPointer(p *int) {
      *p = 50 // 通過指標修改原始值
  }
  modifyThroughPointer(&original)
  ```

## 4. 指標的聲明和初始化
- 使用 `var` 關鍵字和指標數據類型來聲明指標。
- 指標的數據類型格式為 `*Type`，其中 `Type` 是指向的數據類型。
- 可以通過 `&` 符號將變量地址賦值給指標。
  ```go
  var pointer *int
  pointer = &original
  ```

## 5. 指標操作
- 使用 `*` 來讀取或修改指標指向地址的值。
- 在函數調用時，通過傳遞指標參數可以達到修改原始數據的目的。
  ```go
  *pointer = 25 // 修改指向地址的值
  ```

## 6. 使用 `new` 函數創建指標
- `new(Type)` 函數創建一個指向 `Type` 類型的新指標，並返回該指標。
- 使用 `new` 函數創建的指標指向的值具有該類型的默認值。
  ```go
  var newPointer = new(int) // 創建指向 int 的指標，默認值為 0
  ```

## 7. 指標的默認值和錯誤處理
- 未初始化的指標的默認值是 `nil`。
- 對 `nil` 指標進行解引用操作會導致運行時錯誤。
  ```go
  var nilPointer *int // nil 指標
  // 對 nil 指標進行操作會引發運行時錯誤
  ```

