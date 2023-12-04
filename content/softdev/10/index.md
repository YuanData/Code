---
title: "非同步任務實踐：整合Redis與asynq"
draft: false
slug: "class-10"
series: ["軟體開發實務"]
series_order: 10
date: "2289-07-07"
---
10: 本節課解說如何實現非同步任務處理，並利用 Redis 實作任務隊列。

## 非同步任務基本概念
當遇到無法立即處理請求，因為這些請求可能需要較長的時間來完成，並且不希望讓客戶端等待。則可以採用非同步處理機制來處理這類任務，可以使用goroutine在背景中處理任務，但這種策略的缺點是任務基本上存活於進程的記憶體中，若伺服器突然崩潰，則所有未處理的任務將會丟失。

## 採用 Redis 與 asynq 實現任務隊列
比較好的設計是：將任務發送到消息代理的隊列中，然後由一組背景工作程序不斷輪詢隊列以獲取並處理任務。Redis是一個非常受歡迎且高效的消息代理，它將數據存儲在記憶體和持久存儲中。結合Redis集群運行時，可以確信消息代理具有高可用性，並且發送到隊列的任務不會丟失。

## 實現驗證任務的非同步處理
本課程的具體案例是實現發送驗證電子郵件的非同步工作。該過程分為三個步驟：
1. 在資料庫中創建會員記錄
2. 將新的發送電子郵件任務推送到Redis隊列
3. 啟動背景工作程序從隊列中提取任務並處理

為了實現非同步任務處理，採用了一個名為asynq的開源包。asynq是一個簡單、可靠且高效的分散式任務隊列庫，允許使用背景工作程序進行任務的非同步排隊和處理，並以Redis為後盾。實作中首先定義了用於儲存在Redis中的任務數據結構，然後創建了任務分發器接口和相應的Redis任務分發器結構。此外，實現了任務處理器接口和Redis任務處理器結構，並在其中註冊了任務及其處理函數。

## 配置 Redis 並整合到 Go 服務
在app.env檔案中添加Redis伺服器地址的環境變數。在config.go檔案中，將Redis地址添加到Config結構中。在main.go檔案中，添加代碼以連接到Redis。使用asynq.RedisClientOpt{}建立新的Redis選項變數，並創建新的任務分發器。將此任務分發器添加到gRPC伺服器結構中，使其可供所有RPC處理函數使用。
```go
// util/config.go
type Config struct {

	RedisAddress string `mapstructure:"REDIS_ADDRESS"`
}
```

## 實作任務分發與處理
在rpc_create_member.go檔案中，使用任務分發器發出發送驗證電子郵件任務。創建任務有效載荷並進行錯誤處理。
```go
// gapi/rpc_create_member.go
func (server *Server) CreateMember(ctx context.Context, req *pb.CreateMemberRequest) (*pb.CreateMemberResponse, error) {

	taskPayload := &worker.PayloadSendVerifyEmail{
		Membername: member.Membername,
	}
	opts := []asynq.Option{
		asynq.MaxRetry(10),
		asynq.ProcessIn(10 * time.Second),
		asynq.Queue(worker.QueueCritical),
	}
	err = server.taskDistributor.DistributeTaskSendVerifyEmail(ctx, taskPayload, opts...)
	if err != nil {
		return nil, status.Errorf(codes.Internal, "failed to distribute task to send verify email: %s", err)
	}
}
```

## 啟動任務處理器
在main.go檔案中，編寫一個單獨的函數來運行任務處理器。這個函數需要Redis選項和資料庫存儲作為輸入參數。創建新的任務處理器，並在啟動前添加日誌記錄。
```go
// main.go
func runTaskProcessor(redisOpt asynq.RedisClientOpt, store db.Store) {
	taskProcessor := worker.NewRedisTaskProcessor(redisOpt, store)
	log.Info().Msg("start task processor")
	err := taskProcessor.Start()
	if err != nil {
		log.Fatal().Err(err).Msg("failed to start task processor")
	}
}
```
啟動伺服器並測試任務分發和處理。透過呼叫API發送創建會員請求並查看日誌，確認任務是否成功排隊和處理。實現任務延遲、最大重試次數和指定任務隊列。

## 小結
本節課程介紹如何利用Redis和asynq實現非同步背景工作處理機制。通過創建任務分發器和處理器，以及定義任務數據結構，實現了任務的分發、處理以及相關的錯誤處理和日誌記錄。

---
## 課程檔案記錄
{{< ghdiff "843cbda3626ddccf941d083f7d7ffcb05cbe7957" "L" "go.mod,go.sum,config.yaml,gapi/server.go" >}}
