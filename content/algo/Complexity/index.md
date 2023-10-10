---
title: "複雜度"
draft: false
slug: "class-01"
series: ["演算法與解題"]
series_order: 1
---
本節課說明如何透過複雜度衡量程序的運行效率。

## 何謂複雜度
複雜度是評估程序效能的關鍵指標，涵蓋時間和空間兩個維度。它關注於程序對計算時間和空間的需求，並隨著輸入數據量的變化而變化。複雜度表達為 O(f(n)) 形式，f(n) 描述輸入量 n 對資源消耗的影響。

### 程式結構對時間複雜度的影響
時間複雜度直接受到程序結構的影響。例如，順序結構一般是 O(1)，二分查找為 O(logn)，單層循環為 O(n)，而雙層嵌套循環則為 O(n²)。這些模式有助於預測程序的運行效率。

### 降低時間複雜度的重要性
尤其在處理連續用戶請求的在線系統中，減少時間複雜度至關重要。過高的複雜度會導致系統效能不足，進而引起積壓和崩潰。

### 複雜度計算規則
在計算複雜度時，應忽略常數因子，聚焦於較高的複雜度表達式。複雜度分為時間複雜度和空間複雜度，前者與程序結構設計緊密相關，後者則與數據結構選擇有關。

### Go 實例

1. **順序結構（O(1) 複雜度）**:
   ```go
   func calculateArea(length, width int) int {
       return length * width
   }
   ```

2. **二分查找（O(logn) 複雜度）**:
   ```go
   func findItem(items []int, target int) int {
       low, high := 0, len(items)-1
       for low <= high {
           mid := low + (high-low)/2
           if items[mid] == target {
               return mid
           } else if items[mid] > target {
               high = mid - 1
           } else {
               low = mid + 1
           }
       }
       return -1
   }
   ```

3. **單層循環（O(n) 複雜度）**:
   ```go
   func countOccurrences(numbers []int, target int) int {
       count := 0
       for _, num := range numbers {
           if num == target {
               count++
           }
       }
       return count
   }
   ```

4. **雙層嵌套循環（O(n²) 複雜度）**:
   ```go
   func createMatrix(size int) [][]int {
       matrix := make([][]int, size)
       for i := 0; i < size; i++ {
           matrix[i] = make([]int, size)
           for j := 0; j < size; j++ {
               matrix[i][j] = i * j
           }
       }
       return matrix
   }
   ```

## 時間成本較記憶體成本昂貴
程序效能的瓶頸可能發生在時間或空間兩個方面。空間複雜度較容易通過硬體解決，而時間複雜度則更為關鍵，因為它直接影響程序的運行效率和用戶體驗。

優化程序主要包括三個步驟：
    1. 窮舉解法
    2. 無效操作處理
    3. 複雜度抵換。
窮舉解法是找出基本解決方案；無效操作處理則涉及剔除無效計算和存儲，降低複雜度；複雜度抵換則是利用數據結構將時間複雜度轉為空間複雜度，從而提高效率。

### 降低複雜度的案例
這個範例展示了如何找出一個陣列中出現次數最多的元素。我們將使用 Go 語言的字典結構來實現這個邏輯，這樣可以將時間複雜度從 O(n²) 降低至 O(n)。
```go
    func findMostFrequentElement(elements []int) int {
        frequencyMap := make(map[int]int)
        maxCount := 0
        maxElement := -1

        for _, element := range elements {
            frequencyMap[element]++
            if frequencyMap[element] > maxCount {
                maxCount = frequencyMap[element]
                maxElement = element
            }
        }
        return maxElement
    }
```

## 小結
本節提供了對複雜度的深入解析，強調了其在評估程序效能中的重要性。通過掌握時間與空間複雜度的計算及其與程序結構的關聯，可以有效地優化程序，提升效能。