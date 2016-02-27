+++
date = "2016-02-27T20:18:53+09:00"
draft = true
title = "【Rails】whenever導入を通した学び"
tags = ["rails"]
+++

Railsのwheneverを使う機会があったのでその学びをまとめる。
やりたかっとことはRakeタスクを作成し、wheneverを使って定期的にSlackへポストする。

<hr>

## Slack通知

Slackへの通知はすごく簡単。  
ここにだいたい書いてある。

- [RailsアプリからSlackに通知](http://qiita.com/ysk_1031/items/f74c8b8274ec6c58520d)

とりあえず、`config/intializers/slack_api.rb`を作成してtokenを登録しておけば、いろいろできるみたい。

## rakeタスクの作成

初めてrakeタスクを作成した。

- [library rake](http://docs.ruby-lang.org/ja/2.2.0/library/rake.html)


```
$ rake <タスク名> # 指定のタスクを実行。
$ rake <パラメータキー>=<値> <タスク名> # パラメータ指定ありで、指定のタスクを実行。
$ rake -h # 使い方を表示。
$ rake -T # 定義されているタスク一覧を表示
```

(引用:[Rakeの基本的な使い方まとめ|うなの日記](http://unageanu.hatenablog.com/entry/20100829/1283069269))

## whenever

- [javan/whenever|github](https://github.com/javan/whenever)
- [Wheneverは導入が超簡単なcrontab管理ライブラリGemです！](http://morizyun.github.io/blog/whenever-gem-rails-ruby-capistrano/)

## mixpanel

mixpanelのデータのエクスポートしたい。

- [Data Export API](https://mixpanel.com/docs/api-documentation/data-export-api)
- [Rubyでmixpanelのイベントをtrackしたり、データをexportする術](http://kitak.hatenablog.jp/entry/2014/10/07/231003)
