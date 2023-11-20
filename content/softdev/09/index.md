---
title: "實作gRPC"
draft: false
slug: "class-09"
series: ["軟體開發實務"]
series_order: 9
date: "2290-07-07"
---
09: 本節課講解gRPC的基本概念，以及其在後端開發中的應用。

## gRPC簡介
gRPC是的核心概念是允許客戶端在服務器上執行遠程功能，gRPC支持多種編程語言，因此可以從同一API定義輕鬆生成服務器和客戶端程式。首先需使用protocol buffer定義RPC API及其請求/回應結構。然後利用protocol buffer編譯器為服務器和客戶端生成gRPC程式。生成的程式套件括Go的結構及客戶端和服務器的兩個介面，這些可用作之後實際實現的基礎。

### gRPC優勢
gRPC相較於普通的HTTP JSON API有諸多優勢。首先是性能更高，這得益於底層的HTTP/2協議，它提供了諸如二進位格式的記錄數據、多路復用、標頭壓縮和客戶端與服務器間的雙向通信等多項優勢。其次gRPC提供了更好的API合約，服務器和客戶端共享相同的protocol buffer API定義，擁有強類型的請求/回應數據結構。另外自動程式生成是gRPC的一大特色，這使得服務開發更快，因為所有在客戶端與服務器間進行序列化、反序列化或記錄數據的程式都已由gRPC生成和處理。

### gRPC的四種類型
gRPC有四種不同類型：(1)一元gRPC，其中客戶端發送單一請求，服務器回覆單一回應。(2)客戶端流式gRPC，客戶端發送多個訊息流，期望服務器發回單一回應。類似地，(3)服務器流式gRPC和(4)雙向流式gRPC，這兩者分別支持客戶端發送單一請求而服務器回覆多個訊息，以及客戶端和服務器平行且任意順序地發送和接收多個訊息。

## Protobuf 生成 Go 程式
### Protobuf 編譯器安裝與設置
開發gRPC網路服務的首要步驟是利用protobuf定義RPC API及其請求/回應結構，然後使用該protobuf定義生成目標編程語言的服務器和客戶端程式。首先需要安裝protocol buffer編譯器。Linux或Mac用戶可通過套件管理器輕鬆安裝，Windows用戶則需從protoc的發布頁面下載預編譯的二進制檔案。Mac用戶可以使用Homebrew進行安裝。成功安裝後，可通過執行`protoc –version`指令來檢查其版本。

### 安裝 gRPC 插件
為生成Go程式，需安裝兩個編譯器插件：protoc-gen-go插件和protoc-gen-go-grpc插件。protoc-gen-go插件用於生成protobuf中定義的任何請求/回應數據訊息的Go程式；而protoc-gen-go-grpc插件則用於生成與gRPC框架協同工作的Go程式。安裝後，還需將go bin檔案夾添加到系統的PATH中。

### 定義 Protobuf
將定義gRPC服務的protobuf檔案存放於名為proto的檔案夾中。首先定義一個Member物件，設定語法為`proto3`，接著宣告檔案的package（例如命名為pb），並指定要生成的Go package路徑。

定義Member物件套件含多個字段，每個字段都有其數據類型和唯一的字段編號。例如 string類型的membername字段編號為1，timestamp類型的password_changed_time字段編號為4。

### 定義 RPC API
創建一個新檔案rpc_create_member.proto以定義創建新成員的API。定義CreateMemberRequest訊息，其中套件含客戶端創建新成員所需的字段，定義CreateMemberResponse訊息，套件含創建的成員信息。這些訊息類型將被用於服務中的RPC定義。

### 生成Go程式
完成API定義後，接下來是從服務定義生成Go程式。這可以 protoc 指令簡單完成。運行該指令後，將在pb檔案夾中看到生成的.pb.go檔案，這些檔案套件含了gRPC服務器和客戶端介面或stubs，可作為之後實際實現的基礎。

## 運行 gRPC 伺服器
### gRPC 伺服器架構
在先前的講座中，學習了如何使用protobuf定義gRPC API並生成Go程式。本節將學習如何使用這些生成的程式來運行gRPC伺服器。
首先創建一個新的套件（例如命名為gapi）來實現gRPC框架的相同一組服務。在此套件中，創建一個新檔案`server.go`，其中套件含與之前實現的Gin Server類似的Server結構。不同之處在於，這次將服務gRPC請求而非HTTP。從現有的Gin服務程式中複製新伺服器函式程式到新檔案，並進行必要的調整。

### 啟動 gRPC 伺服器
為將伺服器轉換為gRPC伺服器，需使用protoc在前一講中為我們生成的程式。需要實現`AllegroTradeServer`介面，才能成為gRPC伺服器。為確保向前兼容，應將`UnimplementedAllegroTradeServer`結構添加到Server結構中。
在`main.go`檔案中，當前啟動的是HTTP Gin伺服器。另外創建一個單獨的函式來運行gRPC伺服器。在主函式中，調用`runGrpcServer`，並傳入配置和資料庫存儲。使用`grpc.NewServer`創建新的gRPC伺服器物件，然後調用`pb.RegisterAllegroTradeServer`將自己的伺服器實現註冊為gRPC伺服器。

## 實現 gRPC API
### 實現 `CreateMember` 方法
透過實現gRPC API來創建和登入會員。在`UnimplementedAllegroTradeServer`中，可以找到需要實現的`AllegroTradeServer`介面。此介面套件括`CreateMember`和`LoginMember`兩個方法的基本實現。

首先在`gapi`套件中創建一個新檔案`rpc_create_member.go`，並將`CreateMember`方法的程式複製到該檔案中。需要修改此函式的接收器，使其成為服務器物件。`CreateMemberRequest`和`CreateMemberResponse`來自protoc為我們生成的`pb`套件。

接著編寫此方法的真實實現，類似於之前在Gin HTTP API服務器中實現的功能。對於輸入密碼參數，可以直接從`CreateMemberRequest`中獲取。在出現錯誤時，應使用`status.Errorf()`函式返回一個內部錯誤給客戶端。


### 實現 `LoginMember` 方法
為了將資料庫層的`db.Member`物件轉換為gRPC所需的`pb.Member`物件，創建一個`convertMember()`函式，該函式將接收`db.Member`物件並返回`pb.Member`物件。
繼`CreateMember`方法之後，實現`LoginMember`方法。從`UnimplementedAllegroTradeServer`中複製`LoginMember()`方法，並在新檔案`rpc_login_member.go`中進行實現。該方法首先從資料庫中獲取會員信息，檢查輸入的密碼是否與資料庫中的密碼匹配。如果匹配，則創建AccessToken和RefreshToken，並返回`pb.LoginMemberResponse`物件。

---
## 課程檔案記錄
{{< ghdiff "424a061eeffdbd9cee8b5b929cec2d61e1b3d86e" "L" "go.sum,Makefile,gapi/converter.go,config.yaml" >}}