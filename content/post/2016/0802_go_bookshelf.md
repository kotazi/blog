+++
date = "2016-08-02T13:38:10+09:00"
draft = false
tags = ["Go", "GCP"]
title = "【Go】Go Bookshelf App: 概要"

+++

GCPのGoのチュートリアルの概要。

- [Go Bookshelf App](https://cloud.google.com/go/getting-started/tutorial-app)


<hr>


##### 機能:

- リストの閲覧、追加、削除
- 詳細の編集
- カバー画像のアップロード
- Googleアカウントでのログインと、追加した本の確認

##### 目標:

- サンプルアプリケーションのクローン
- ローカルでの起動
- GAE環境へのデプロイ
- サンプルコードを読む
- データ構造のストアについて
- Google Cloud Storeでのバイナリデータのストアについて
- ユーザー認証について
- GCP Consoleで確認するイベントログについて
- バックグラウンドワーカーにタスクを送るGoogle Cloud Pub/Subの使い方

##### 手順:

[ここ](https://console.cloud.google.com/project?_ga=1.189765870.459013261.1468980200)からGCP Console Projectを選択/生成する。
[ここ](https://support.google.com/cloud/answer/6293499#enable-billing)を見て課金を有効にする。
さらに、[Cloud SDK](https://cloud.google.com/sdk/docs/)のインストール。


それができたら、下記コマンドで`gcloud`認証

```
gcloud init
```

成功すると[ここ](https://cloud.google.com/sdk/auth_success)へ飛ぶ。(gcloudのチュートリアルあるから読んでみてみいいかも。)

[ここ](https://console.cloud.google.com/flows/enableapi?apiid=datastore.googleapis.com,datastore,pubsub,storage_api,logging,plus&_ga=1.180912098.459013261.1468980200)からDatastore、Cloud Pub/Sub, Cloud Storage JSON, Cloud Logging, Google+ APIsを利用できるようにする

続いて、アカウントを確認

```
~/R/s/gcp ❯❯❯ gcloud config list
Your active configuration is: [default]

[core]
account = kotazi@*****.com
disable_usage_reporting = False
project = ********
```

必要に応じて、自分で設定もできる

```
gcloud config set project [YOUR_PROJECT_ID]
```

サンプルリポジトリのクローンします。

```
go get -u -d github.com/GoogleCloudPlatform/golang-samples/getting-started/bookshelf/app
```

`aedeploy`コマンドをインストールする

```
go get -u google.golang.org/appengine/cmd/aedeploy
```

以上で準備が完了。

ここからGoogle Cloud Platformのさまざまな機能を試していく。
