---
title: "排序"
draft: false
slug: "class-07"
series: ["演算法與解題"]
series_order: 7
date: "2192-07-07"
---
07: 本節課深入解析冒泡排序、插入排序、歸併排序及快速排序的原理，並比較它們的優劣。

## 排序算法的原理與比較
排序是將無序數據轉化為有序過程的算法問題，常見於計算機科學中。對排序算法的評估通常基於時間複雜度、空間複雜度和穩定性三個維度。

### 冒泡排序
1. **原理**: 逐一比較相鄰元素，將較大元素往後移動，直至無需交換。
2. **性能**: 最好時間複雜度 O(n)，最壞和平均 O(n^2)；空間複雜度 O(1)；穩定性良好。

### 插入排序
1. **原理**: 從未排序區間選取元素插入已排序區間的適當位置。
2. **性能**: 最好時間複雜度 O(n)，最壞和平均 O(n^2)；空間複雜度 O(1)；穩定。

### 歸併排序
1. **原理**: 採用分而治之，將數據分為較小子集合後排序並合併。
2. **性能**: 時間複雜度恆為 O(nlogn)；空間複雜度 O(n)；穩定。

### 快速排序
1. **原理**: 基於分而治之，以特定元素為基準劃分陣列，再分別對子區間進行排序。
2. **性能**: 最好和平均時間複雜度 O(nlogn)，最壞 O(n^2)；空間複雜度 O(1)；不穩定。

### Go語言實現快速排序
以下是使用 Go 語言實現快速排序的示例。

```go
func quickSort(arr []int, low, high int) {
    if low >= high {
        return
    }
    pivot := partition(arr, low, high)
    quickSort(arr, low, pivot-1)
    quickSort(arr, pivot+1, high)
}

func partition(arr []int, low, high int) int {
    pivot := arr[high]
    i := low - 1
    for j := low; j < high; j++ {
        if arr[j] < pivot {
            i++
            arr[i], arr[j] = arr[j], arr[i]
        }
    }
    arr[i+1], arr[high] = arr[high], arr[i+1]
    return i + 1
}

func main() {
    arr := []int{10, 7, 8, 9, 1, 5}
    quickSort(arr, 0, len(arr)-1)
    fmt.Println("Sorted array: ", arr)
}
```

# 小結
排序算法是解決數據處理問題的基礎，選擇合適的排序算法依據數據規模和資源限制。對小規模數據，O(n^2) 算法可行；對大規模數據，O(n log n) 算法更優。歸併排序提供穩定性但耗費較多空間，而快速排序則在大多數情況下提供更好的性能但缺乏穩定性。有效利用這些算法對於數據處理和分析至關重要。