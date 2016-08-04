+++
date = "2016-08-05T13:38:10+09:00"
draft = true
tags = ["Go", "GCP"]
title = "【Go】Go Bookshelf App: Authenticating Users"

+++

OAuth2.0を使ったユーザー認証。

- [Authenticating Users with Go](https://cloud.google.com/go/getting-started/authenticate-users)

<hr>

### Web application client IDの作成

[credentials](https://console.cloud.google.com/apis/credentials)へアクセスし、`OAuth consent screen`をクリック。
プロダクト名のところに`Go Bookshelf App`と入力し、その他も任意で記入する。

保存後、`Create credentials > OAuth client ID`と移動し、`Application type`では`Web application`を選択。
`Name`は`Go Bookshelf Client`。`Authorized redirect URIs`にProjectIDに置き換えた値を入れる。

```
http://localhost:8080/oauth2callback
http://[YOUR_PROJECT_ID].appspot.com/oauth2callback
```

作成すると、IDとclient secretが表示されるので保存しておくこと。

<hr>

前回同様、下記へ移動して`config.go`を開く。

```
cd $GOPATH/src/github.com/GoogleCloudPlatform/golang-samples/getting-started/bookshelf
```

下記のコメントを削除し、clientidとclientsecretを先ほどの値に置き換える。
```
// oauthConfig = configureOAuthClient("clientid", "clientsecret")
```

`app/app.yaml`を開き、`OAUTH2_CALLBACK`のProjectIDを自分のものに置き換える。

<hr>

ローカルの立ち上げやデプロイは前回同様。

![](https://cloud.google.com/go/images/go-auth.png)

セッションを扱うために[Gorilla web toolkit](http://www.gorillatoolkit.org/)を使っている。
Gorillaはいくつかの`Store`インターフェイスを持っているが、今回は`CookieStore`を利用。


認証の仕組みはまず、Googleの認証画面にリダイレクトさせ、そのレスポンスを、ユーザーがアプリケーションに戻る際に処理させるというものである。

アプリケーションはClientIDとOAuthスコープを使ってURLを生成する。スコープには、アプリケーションがアクセスしようとするユーザー情報が何かということを含む。

`config.go`のこの部分がそれにあたる。

```
func configureOAuthClient(clientID, clientSecret string) *oauth2.Config {
        redirectURL := os.Getenv("OAUTH2_CALLBACK")
        if redirectURL == "" {
                redirectURL = "http://localhost:8080/oauth2callback"
        }
        return &oauth2.Config{
                ClientID:     clientID,
                ClientSecret: clientSecret,
                RedirectURL:  redirectURL,
                Scopes:       []string{"email", "profile"},
                Endpoint:     google.Endpoint,
        }
}
```

認証が完了するとGoogle認証サービスは`/oauth2callback`を通してユーザーをアプリケーションへ戻す。

認証後はGoogle+ APIが利用できるため`plusService.People.Get("me")`を使うと認証済みのユーザーの情報が取得できる。
