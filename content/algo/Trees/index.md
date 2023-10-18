---
title: "樹"
draft: false
slug: "class-06"
series: ["演算法與解題"]
series_order: 6
date: "2193-07-07"
---
06：本節課專注於樹和二元樹的結構及其在數據處理中的應用。

## 樹和二元樹
樹是一種層次化的數據結構，由結點和邊組成，沒有環路。它包括根結點、父子結點關係、葉子結點等。樹的每個結點可以有多個子結點，形成一種“一對多”的關係。

二元樹是樹的一種特殊形式，其中每個結點最多有兩個子結點，稱為左子結點和右子結點。

### 樹的基本特性和二元樹的變體
樹的特性包括結點間的層次關係和遞迴性質，而二元樹的特殊變體如滿二元樹和完全二元樹，具有更嚴格的結構限制。樹的結構特點使其在存儲層次化數據和實現數據分類時特別有效。

### 樹的操作：增刪查
樹的操作主要包括增加、刪除和查找。這些操作通常通過遞迴方法實現，並以前序、中序和後序三種方式進行遍歷。在二元樹中，這些操作的效率受到樹結構的影響，尤其是在二元查找樹中，查找操作的效率可達到 O(logn)。

## Go語言實現樹的基本操作
以下是使用 Go 語言實現樹結構和基本操作的示例。

```go
type TreeNode struct {
    Val   int
    Left  *TreeNode
    Right *TreeNode
}

// 插入節點
func (t *TreeNode) Insert(val int) {
    if t == nil {
        return
    } else if val <= t.Val {
        if t.Left == nil {
            t.Left = &TreeNode{Val: val}
        } else {
            t.Left.Insert(val)
        }
    } else {
        if t.Right == nil {
            t.Right = &TreeNode{Val: val}
        } else {
            t.Right.Insert(val)
        }
    }
}

// 搜尋節點
func (t *TreeNode) Search(val int) bool {
    if t == nil {
        return false
    }
    if val < t.Val {
        return t.Left.Search(val)
    } else if val > t.Val {
        return t.Right.Search(val)
    }
    return true
}
```

# 小結
樹和二元樹是處理層次化數據結構的重要工具，特別是在需要表示數據間“一對多”關係的場景中。樹結構的增刪查操作雖然在一般情況下效率一般，但在二元查找樹中，這些操作的效率可以顯著提高。樹結構廣泛應用於數據存儲、信息檢索、決策支持系統等領域，是現代計算機科學中不可或缺的組件。