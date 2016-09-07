+++
date = "2016-09-06T13:38:10+09:00"
draft = false
tags = ["Webpack"]
title = "【Webpack】devtoolのこと"

+++

Webpackでビルドしたファイルのデバッグ方法が気になっていたので少し調べてみた。

<hr>

結論からいうと`webpack.config.js`の`devtool`オプションから設定できる。
さらに言えば開発環境では`cheap-module-eval-source-map`、本番環境では`cheap-module-source-map`を設定するのが良い。

- [configuration | Webpack](http://webpack.github.io/docs/configuration.html#devtool)
- [Webpack devtool source map « Cheng's Blog](http://cheng.logdown.com/posts/2016/03/25/679045)

本番環境をサポートしているものとしていないものがあるのでその点は注意すること。
