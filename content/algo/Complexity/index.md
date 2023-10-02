---
title: "效能評估：複雜度"
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

## Go 實例

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

## 小結
本節提供了對複雜度的深入解析，強調了其在評估程序效能中的重要性。通過掌握時間與空間複雜度的計算及其與程序結構的關聯，可以有效地優化程序，提升效能。