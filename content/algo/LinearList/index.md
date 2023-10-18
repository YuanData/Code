---
title: "線性表"
draft: false
slug: "class-02"
series: ["演算法與解題"]
series_order: 2
date: "2197-07-07"
---
02：本節課著重介紹線性表的結構和特點。

## 線性表的基本原理
線性表是一系列數據元素的集合，其中數據元素可以是任意類型。線性表最典型的實現是鏈表，包括單向鏈表、雙向鏈表和循環鏈表。單向鏈表中的每個節點包含數據部分和指向下一節點的指標，而雙向鏈表則在每個節點中增加了指向前一節點的指標。

## 鏈表的增刪查操作

### 增加操作
在鏈表中添加新節點是通過調整指標來實現的。假設我們要在節點 `p` 之後添加一個新節點 `s`，我們只需將 `s.next` 指向 `p.next`，然後將 `p.next` 指向 `s`。

### 刪除操作
刪除鏈表中的節點也是通過調整指標實現的。如果要刪除 `p.next` 指向的節點，只需將 `p.next` 更新為 `p.next.next`。

### 查找操作
鏈表的查找操作涉及遍歷鏈表。根據查找條件的不同，可能需要遍歷整個鏈表，這在時間複雜度上是 O(n)。

## Go實現範例
以下是用 Go 語言實現的鏈表操作示例。

```go
type Node struct {
    Value int
    Next  *Node
}

// 在節點 p 之後添加新節點
func insertAfter(p, newNode *Node) {
    newNode.Next = p.Next
    p.Next = newNode
}

// 刪除節點 p 之後的節點
func deleteAfter(p *Node) {
    if p.Next != nil {
        p.Next = p.Next.Next
    }
}

// 查找特定值的節點
func search(head *Node, value int) *Node {
    current := head
    for current != nil {
        if current.Value == value {
            return current
        }
        current = current.Next
    }
    return nil
}
```

# 小結
本節課涵蓋了線性表的基礎知識，包括其結構、類型及如何在鏈表中實現數據的增加、刪除和查找操作。這些基礎知識對於理解數據結構和算法非常重要。