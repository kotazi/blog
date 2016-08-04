+++
date = "2016-08-03T13:38:10+09:00"
draft = false
tags = ["Go", "GCP"]
title = "【Go】Go Bookshelf App: Datastore"

+++

GCPのGoのチュートリアルの概要。  
とりあえずDatastoreでやってみることに。

- [Using Structured Data with Go](https://cloud.google.com/go/getting-started/using-structured-data)


<hr>


先ほどクローンしたコードの編集を加える。

```
cd $GOPATH/src/github.com/GoogleCloudPlatform/golang-samples/getting-started/bookshelf
```

`config.go`を開き、下記部分のコメントを消して、自分のプロジェクトIDに置き換える。

```
// DB, err = configureDatastoreDB("projectid")
```

### ローカルマシンで起動

下記コマンドでローカルマシンを立ち上げる。

- [http://localhost:8080](http://localhost:8080)

```
cd app
go run app.go auth.go template.go
```

アプリケーションの動作を確認できる。


### デプロイする

`app`ディレクトリ内で下記のコマンド。`https://[YOUR_PROJECT_ID].appspot.com`でアクセス可能。

```
aedeploy gcloud app deploy
```

アプリを更新する場合、同じコマンドをを使えばバージョンを更新できる。
新しくデプロイすると新しいバージョンが作られる。

古いバージョンも残り続けるので課金対象にしたくなければ削除するのがいい。

[バージョンページ](https://console.cloud.google.com/appengine/versions)から削除できる。

<hr>

![](https://cloud.google.com/go/images/go-datastore.png)

`getting-started/bookshelf/db_datastore.go`の

```
// AddBook saves a given book, assigning it a new ID.
func (db *datastoreDB) AddBook(b *Book) (id int64, err error) {
        ctx := context.Background()
        k := datastore.NewIncompleteKey(ctx, "Book", nil)
        k, err = db.client.Put(ctx, k, b)
        if err != nil {
                return 0, fmt.Errorf("datastoredb: could not put Book: %v", err)
        }
        return k.ID(), nil
}
```

この辺りがDatastoreに保存する処理。


ちなみに`:=`は変数宣言と代入を行っている。
