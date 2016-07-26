+++
date = "2016-07-26T11:57:40+09:00"
draft = false
tags = ["shell"]
title = "【Shell】ちょっとした覚書"

+++

[JamieMason/ImageOptim-CLI](https://github.com/JamieMason/ImageOptim-CLI)のサンプルにあるシェルが読めなかったので調べた。

```
images=$(git diff --exit-code --cached --name-only --diff-filter=ACM -- '*.png' '*.jpg')
$(exit $?) || (echo "$images" | imageoptim && git add $images)
```

↑ これ。

<hr>

- [bash Tips - コマンド置換と算術式展開、パラメータ展開](http://qiita.com/mashumashu/items/bbc3a79bc779fe8c4f99)

`$()`で囲むとコマンド実行できる。

- [git-diff(1) - Linux man page](http://linux.die.net/man/1/git-diff)

`-exit-code`は差分があれば`1`を返すし、なければ`0`

`--cached`はステージしたファイルの差分を確認する。

`--name-only`は差分のあるファイル名のみ出力する。

`--diff-filter`は`ACDMRTUXB*`から選べる。
今回の`ACM`はAdded (A), Copied (C), Deleted (D), Modified (M), Renamed (R), changed (T), are Unmerged (U), are Unknown (X), Broken (B)である。
今回はAdded/Copied/Modified。

2行目の`$?`。これは直前に実行された処理の終了コード値を表す。  
参考: [Linuxのシェルスクリプト変数の記号あれこれ](http://kajitiluna.hatenablog.com/entry/20111023/1319381392)

`0`もしくは`1`が渡るはず。  
`exit 0`(差分がなかった場合)はそのまま正常終了。  
`exit 1`(差分があった場合)は次の処理へ進む。

つまり、`(echo "$images" | imageoptim && git add $images)`を実行する。  
`$images`は1行目の変数を表していて、つまり差分のあるファイル名。`|`(パイプ)を使ってる。

参考: [パイプ「|」を使って 複数のコマンドを組み合わせる](http://webkaru.net/linux/commands-pipeline/)
