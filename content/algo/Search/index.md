---
title: "搜尋遍歷"
draft: false
slug: "class-10"
series: ["演算法與解題"]
series_order: 10
date: "2089-07-07"
---
10：本節課介紹深度優先搜索（DFS）和廣度優先搜索（BFS）。

## 深度優先搜索（DFS）
深度優先搜索（DFS）類似於迷宮探索。它採用递归或堆疊來實現回溯，適合解決分解為多個子問題的場景。

## DFS 的 Go 實現

```go
func dfs(graph [][]int, node int, visited []bool) {
    visited[node] = true
    // 處理當前節點的業務邏輯
    fmt.Println("訪問節點：", node)

    for _, neighbor := range graph[node] {
        if !visited[neighbor] {
            dfs(graph, neighbor, visited)
        }
    }
}
```

## 廣度優先搜索（BFS）
廣度優先搜索（BFS）適合解決最短路徑或層次遍歷問題。它使用隊列來實現廣度優先的遍歷。

## BFS 的 Go 實現

```go
func bfs(graph [][]int, start int) {
    queue := make([]int, 0)
    visited := make([]bool, len(graph))
    queue = append(queue, start)
    visited[start] = true

    for len(queue) > 0 {
        node := queue[0]
        queue = queue[1:]
        fmt.Println("訪問節點：", node)

        for _, neighbor := range graph[node] {
            if !visited[neighbor] {
                queue = append(queue, neighbor)
                visited[neighbor] = true
            }
        }
    }
}
```

## 小結
1. **選擇 DFS**：當問題可以分解為多個子問題，子問題的解可能影響整體問題的解。
2. **選擇 BFS**：當需要在圖或樹中找到從源節點到目標節點的最短路徑。
