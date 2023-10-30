---
title: "堆疊"
draft: false
slug: "class-03"
series: ["演算法與解題"]
series_order: 3
date: "2196-07-07"
---
03: 本節課探討堆疊的定義和特點。

## 堆疊
堆疊（Stack）是一種特殊的線性表，其特點在於限定新增和刪除操作只能在表的一端進行，即後進先出（LIFO）原則。這種結構類似於堆疊磚塊，只能從頂部添加或移除。堆疊的這種操作限制，儘管降低了靈活性，但在特定應用中能提高效率，如瀏覽器的前進和後退功能。

### 順序堆疊的操作
順序堆疊通過陣列實現，使用一個指標 `top` 表示堆頂元素位置。新增（Push）操作將元素加在堆頂，刪除（Pop）操作從堆頂移除元素。查找操作需要遍歷整個堆疊，其時間複雜度與線性表相同。

### 鏈堆疊的操作
鏈堆疊利用鏈表實現，其中堆頂設置在鏈表的頭部。新增和刪除操作均在堆頂進行，時間複雜度為 O(1)。與順序堆疊相同，查找操作也需遍歷整個堆疊。

## Go語言實現堆疊
以下是用 Go 語言實現的堆疊操作示例。

```go
type Stack struct {
    items []int
}

// 新增元素到堆疊
func (s *Stack) Push(item int) {
    s.items = append(s.items, item)
}

// 從堆疊中移除元素
func (s *Stack) Pop() int {
    length := len(s.items)
    if length == 0 {
        return -1 // 堆疊為空
    }
    item := s.items[length-1]
    s.items = s.items[:length-1]
    return item
}

// 檢查堆疊是否為空
func (s *Stack) IsEmpty() bool {
    return len(s.items) == 0
}
```

# 小結
堆疊是一種特殊的線性表，採用後進先出的操作原則。它在特定應用中，如瀏覽器的前進後退功能，可以提供高效率的解決方案。堆疊的基本操作包括新增、刪除和查找，其中新增和刪除操作通常具有 O(1) 的時間複雜度，而查找操作則需要 O(n)。通过限制操作的自由度，堆疊在特定场景下更加高效。