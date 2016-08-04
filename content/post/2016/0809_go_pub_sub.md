+++
date = "2016-08-02T17:41:51+09:00"
description = ""
title = "0809_go_pub_sub"

+++

- [Using Cloud Pub/Sub with Go](https://cloud.google.com/go/getting-started/using-pub-sub)

[Google Book API](https://developers.google.com/books/)を使ってバックグラウンドワーカーに処理をさせる。

<hr>

前回同様、`config.go`から下記部分のコメントを削除し、ProjectIDを書き換える。

ローカルホストを立ち上げた状態で下記のコマンドでバックグラウンドワーカーが動く。

```
cd $GOPATH/src/github.com/GoogleCloudPlatform/golang-samples/getting-started/bookshelf
cd pubsub_worker
PORT=8081 go run worker.go
```

`http://localhost:8081/`でワーカーインスタンスに立直できる。
処理した本の数についてのステータスが確認できる。


<img src="https://www.evernote.com/l/Aj24JXT3jO1BGJQHmgGX0QAoHCxcg56AdDIB/image.png" alt="Pasted%20Image%208/3/16,%2011:14" />

↑ 「イノベーションのジレンマ」とタイトルをいれると勝手に入る


### デプロイする

```
$ cd $GOPATH/src/github.com/GoogleCloudPlatform/golang-samples/getting-started/bookshelf

# Deploy the worker
cd pubsub_worker
aedeploy gcloud app deploy

# Deploy the main app
cd ../app
aedeploy gcloud app deploy
```

下記がアクセス先になる。

```
https://[YOUR_PROJECT_ID].appspot.com
https://worker-dot-[YOUR_PROJECT_ID].appspot.com
```

![](https://cloud.google.com/go/images/go-pub-sub.png)

データベース内で本が更新されるとCloud Pub/Subが走る。
ワーカーは分離して稼働していて、これらのイベントをリッスンしている。
イベントを受け取るとワーカーkはBooksAPIにリクエストを作成し、データベースの本の記録を更新する。

以上。
