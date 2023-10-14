---
title: "遞迴"
draft: false
slug: "class-09"
series: ["演算法與解題"]
series_order: 9
---
本節課介紹了利用遞迴方法解決經典的河內塔問題，打開算法思維的學習之路。

## 遞迴
遞迴是一種在函式內部調用自身的方法，常見於數學與計算機科學領域。它依靠將大問題分解為較小且相似的子問題，並通過基礎情況的解決來逐步解決整體問題。遞迴的核心在於遞迴主體的設計和明確的終止條件。

### 遞迴的基本原則
遞迴方法在解決問題時，需符合兩個基本原則：問題可分解為相同形式的子問題，且存在明確的終止條件。這種方法特別適用於那些可以通過重複相同操作來縮小問題規模的場景。

### 河內塔問題的遞迴解法
河內塔問題是一個經典的遞迴問題示例。問題要求將一系列大小不同的圓環從一根柱子移動到另一根，遵循一定規則：一次只移動一個圓環，且大圓環不能放在小圓環上。通過遞迴思想，此問題可分解為移動較小圓環群組和移動最大圓環的子問題。

### Go語言實現河內塔遞迴解法
以下是使用 Go 語言實現河內塔問題遞迴解法的示例。

```go
func hanoi(n int, x, y, z string) {
    if n == 1 {
        fmt.Printf("Move: %s -> %s\n", x, z)
    } else {
        hanoi(n-1, x, z, y)
        fmt.Printf("Move: %s -> %s\n", x, z)
        hanoi(n-1, y, x, z)
    }
}

func main() {
    hanoi(3, "A", "B", "C")
}
```

# 小結
遞迴是一種強大且靈活的算法設計方法，特別適合解決那些可以自然分解為較小同類問題的場景。它通過簡化問題，逐步降低問題的規模，直到達到最基本的情況，從而找到整體問題的解決方案。河內塔問題是遞迴思維的一個經典應用案例，展示了如何將一個看似複雜的問題分解為易於管理的子問題。這種思維方式在計算機科學中廣泛應用，是理解和應對複雜問題的關鍵工具。