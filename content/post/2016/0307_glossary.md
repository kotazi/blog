+++
date = "2016-03-06T19:21:07+09:00"
draft = true
tags = []
title = "0307_glossary"

+++

typeを使ったReduxの用語集。
typeについては[こちら](http://flowtype.org/docs/quick-reference.html)

## State(ステート)

```
type State = any
```

State(State treeとも呼ばれる)は広い言葉であるが、
Redux APIにおいてはたいていStoreの管理する単一のStateを指し、
`getState()`で取得できる値のことである。
多くの場合、深くネストされたオブジェクトであり、Reduxアプリケーションを全体のStateを象徴するものである。

慣習的にトップレベルのStateはオブジェクト、もしくはMapのようなキーと値のCollectionが用いられるが、
技術的にはどんな型でも可能である。Stateはシリアライズ可能であるべきだ。
JSONに変換できないようなものを内側に置いてはならない。

## Action(アクション)

```
type Action = Object
```

ActionはStateを変更するために用いられるプレーンオブジェクトだ。
Actionのみが取得したデータをStoreに入れることができる。
UIイベントからのもの、コールバック、WebSocketなどどんなデータも結局のところ
Actionとしてディスパッチされる。

Actionは`type`を持つ。typeは定数として定義され別モジュールからインポートする。
シリアライズの理由で[Symbol](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Symbol)よりも`type`を使ったほうがいい。
