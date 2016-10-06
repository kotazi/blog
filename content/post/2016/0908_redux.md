+++
date = "2016-09-06T13:38:10+09:00"
draft = true
tags = ["Webpack"]
title = "【Redux】Redux要点まとめ"

+++


## API

### ■ `createStore`

アプリのステートツリーを保持するRedux storeを生成する。
アプリ内にひとつであるべき。


|引数|説明|
|---|---|
|reducers(Function)||
|[preloadState]\(any)||
|[enhancer]\(Function)||
