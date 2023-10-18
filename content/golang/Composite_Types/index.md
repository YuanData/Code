---
title: "組合型別"
draft: false
slug: "class-07"
series: ["Go基礎入門"]
series_order: 7
date: "2092-07-07"
---
07：本節課介紹了 Go 組合型別：陣列、slice和map的操作與遍歷。

## 陣列

### 陣列（Array）基本概念
- **定義**：陣列是一組長度固定，元素類型固定的序列。

### 陣列宣告與賦值
- **宣告**：指定陣列的長度和元素類型。
- **預設值**：陣列擁有預設值，基於它是值類型。
- **賦值方式**：可以在宣告時賦值或透過索引逐一賦值。

```go
var array1 [3]int             // 宣告一個長度為3的int陣列
array1[0] = 1                 // 透過索引賦值
array2 := [3]int{1, 2, 3}     // 宣告並初始化陣列
```

### 陣列遍歷
- **使用for循環**：標準的遍歷方法，使用 `len(array)` 獲取陣列長度。
- **使用for...range**：簡化遍歷，自動處理索引和值。

```go
for i := 0; i < len(array1); i++ {
    fmt.Println(array1[i])
}

for index, value := range array2 {
    fmt.Println(index, value)
}
```

## Slice

### Slice（切片）基本概念
- **定義**：Slice 是對陣列的參照，擁有動態大小。
- **宣告**：可以透過指定範圍創建 Slice。

```go
array3 := [5]int{1, 2, 3, 4, 5}
slice1 := array3[1:4]  // 創建 Slice，參照 array3 的一部分
```

### Slice 操作
- **修改 Slice 元素**：會影響原陣列。
- **使用 make 函式**：動態創建 Slice，指定長度和容量。
- **使用 append 函式**：動態追加元素，必要時會擴充底層陣列。

```go
slice1[0] = 10              // 修改 Slice，影響原陣列
slice2 := make([]int, 3, 5) // 使用 make 創建 Slice
slice2 = append(slice2, 4)  // 追加元素
```
當然，以下是有關在 Go 語言中遍歷 Slice 的 Markdown 筆記和範例代碼：

### Slice 遍歷

#### 使用 for 循環遍歷 Slice
- **標準 for 循環**：可以使用 `len(slice)` 獲取 Slice 的長度，並透過索引訪問每個元素。

```go
slice := []int{1, 2, 3, 4, 5}
for i := 0; i < len(slice); i++ {
    fmt.Println(slice[i])
}
```

#### 使用 for...range 遍歷 Slice
- **for...range**：簡化遍歷方式，自動提供每個元素的索引和值。

```go
for index, value := range slice {
    fmt.Printf("Index: %d, Value: %d\n", index, value)
}
```

#### 忽略索引或值
- **僅遍歷值**：如果不需要索引，可以使用下劃線 `_` 忽略它。

```go
for _, value := range slice {
    fmt.Println(value)
}
```

## Map

### Map 基本概念
- **定義**：Map 是一種無序的鍵值對集合。
- **特性**：
  - 鍵（Key）必須是可比較的基本數據類型。
  - 值（Value）可以是任何類型。
  - Map 是參照類型。

### Map 的宣告與初始化
- **宣告**：使用 `var` 關鍵字，指定鍵和值的類型。
- **初始化**：使用 `make` 函式或直接賦值初始化 Map。

```go
var map1 map[string]string        // 宣告一個map
map1 = make(map[string]string, 2) // 初始化map，可選擇指定初始容量

// 直接初始化
map2 := map[string]string{
    "morning": "coding",
    "noon":    "delivering",
    "night":   "driving",
}
```

### Map 的操作
- **添加與修改元素**：使用 `map[key] = value` 方式。
- **檢查元素是否存在**：使用雙賦值方式檢查鍵是否存在於 Map 中。

```go
// 添加元素
map1["morning"] = "coding"

// 檢查並獲取元素
value, ok := map2["morning"]
if ok {
    fmt.Println("Value:", value)
} else {
    fmt.Println("Key not found")
}
```

### Map 的刪除與清空
- **刪除元素**：使用 `delete(map, key)` 函式。
- **清空 Map**：將 Map 設為 nil 或重新 make。

```go
delete(map1, "night") // 刪除 key 為 "night" 的元素

map1 = nil // 清空 map1
map2 = make(map[string]string) // 重新初始化 map2
```

### Map 遍歷
- **使用 for...range**：遍歷 Map，獲取每個鍵值對。
  
```go
for key, value := range map2 {
    fmt.Println(key, value)
}
```
