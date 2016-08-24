+++
date = "2016-08-08T13:38:10+09:00"
draft = false
tags = ["Go", "GCP"]
title = "【Go】Go Bookshelf App: Logging"

+++

どのようにして詳細なログ情報を残していくか。

- [Logging Application Events with Go](https://cloud.google.com/go/getting-started/logging-application-events)


Cloud Platformの提供するログやモニタリングツールがあるので、Cloud Platform Consoleから簡単に確認することができる。

このサンプルでは標準の`log`パッケージとGorillaの`CombinedLoggingHandler`を利用している。

標準のエラー:

```
type appHandler func(http.ResponseWriter, *http.Request) *appError

type appError struct {
        Error   error
        Message string
        Code    int
}

func (fn appHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
        if e := fn(w, r); e != nil { // e is *appError, not os.Error.
                log.Printf("Handler error: status code: %d, message: %s, underlying err: %#v",
                        e.Code, e.Message, e.Error)

                http.Error(w, e.Message, e.Code)
        }
}
```

HTTPリクエストのログ:

```
// Delegate all of the HTTP routing and serving to the gorilla/mux router.
// Log all requests using the standard Apache format.
http.Handle("/", handlers.CombinedLoggingHandler(os.Stderr, r))
```

ログは[ここ](https://console.cloud.google.com/logs/viewer)から見れる。
