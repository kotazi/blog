+++
date = "2016-08-10T13:38:10+09:00"
draft = false
tags = ["Go", "GCP"]
title = "【Go】Deploying a Go App"

+++

GCP/Goアプリケーションのデプロイ周りについて学んでいたところ。  
`goapp deploy`だったり、GoogleAppEngineLauncherだったり、
`appcfg.py`だったり、`dev_appserver.py`だったりと少しややこしくなった。

- [Deploying a Go App](https://cloud.google.com/appengine/docs/go/tools/uploadinganapp#top_of_page)

まず、`goapp`は[App Engine Go SDK](https://cloud.google.com/appengine/downloads#Google_App_Engine_SDK_for_Go)
に含まれているものだ。`appcfg.py`もここに含まれている。

デプロイ後、アプリケーションは`https://<YOUR-PROJECT-ID>.appspot.com`で動作する。

ファイルをアップロードするには、`goapp deploy`をアプリケーションのルートディレクトリから実行しよう。この時ルートディレクトリには
`app.yaml`が用意されていなければならない。
引数を使ってルートの位置を示すこともできるけれども、細かいことは`goapp help deploy`で見てほしい。


`app.yaml`にあるアプリケーションIDを自動的に使う。けれども多くのサンプルアプリケーションでは`app.yaml`から`application:`や
`version:`を除外している。そういう場合はコンソールからバージョンやアプリケーションIDを指定する。

```
goapp deploy -application <YOUR_PROJECT_ID> -V v1 myapp/
```

アプリケーションが複数の[サービス](https://cloud.google.com/appengine/docs/go/configuration-files)から成る場合、
更新するサービスを指定することができる。

- デフォルトのサービスを更新する場合、リストの最初ものが更新される
- 初めて更新する場合、デフォルトのサービスは必ず含まれる
