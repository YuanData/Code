---
title: "介面"
draft: false
slug: "class-13"
series: ["Go基礎入門"]
series_order: 13
---
本節課介紹Go介面的基本概念、應用場景、宣告和實現方式。

## 介面的基本概念
- 介面是一種特殊的數據類型，用於定義方法的集合。
- 介面的用途在於提供一個統一的方式來處理不同的數據結構。

## 介面的應用場景
- 想像有不同的消息類型，例如文字消息和圖片消息，並且每種消息類型都有專門的設置方法。
- 為了能夠通過一個共用的函式發送不同類型的消息，需要一個統一的介面。

## 介面的宣告與實現
- 介面宣告包含了方法的定義，但不實現這些方法。
- 不同的數據類型（如文字消息和圖片消息）可以實現相同的介面。

## Go代碼範例

### 文字消息和圖片消息的結構體
```go
type TextMessage struct {
    content string
}

func (t *TextMessage) SetContent(content string) {
    t.content = content
}

type ImageMessage struct {
    imagePath string
}

func (i *ImageMessage) SetImage(path string) {
    i.imagePath = path
}
```

### 介面的宣告與方法
```go
type Message interface {
    SetType() string
}
```

### 介面實現
```go
func (t *TextMessage) SetType() string {
    return "Text Message"
}

func (i *ImageMessage) SetType() string {
    return "Image Message"
}
```

### 介面的使用
```go
func SendMessage(m Message) {
    fmt.Println("Sending message type:", m.SetType())
}

func main() {
    textMsg := &TextMessage{}
    imageMsg := &ImageMessage{}

    SendMessage(textMsg)
    SendMessage(imageMsg)
}
```
