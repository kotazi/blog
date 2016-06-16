+++
date = "2016-06-16T13:50:34+09:00"
draft = false
tags = ["git", "bfg"]
title = "【git/bfg】間違えてプッシュしたパスワードファイルなどを履歴から完全に削除する"

+++

AWS情報の書いてあるJSONファイルが間違えてコミットしてあったため、
その削除方法について調べてトライしました。

<hr>

- [Remove sensitive data](https://help.github.com/articles/remove-sensitive-data/)

上記のリンクに方法が書いてある。  
ざっくりまとめると2つの方法があって、

1. [BFG Repo-Cleaner](https://rtyley.github.io/bfg-repo-cleaner/)を使う方法
2. コマンドを叩いて頑張る方法

の2つがある。どちらかと言えば1を推奨しているよう。

<hr>

## BFGを使って削除する

Scala製なのでJavaの実行環境が必要になる。  
今回は`.zprofile`に`alias bfg='java -jar /Applications/bfg-1.12.12.jar'`としてエイリアスを作成。

```
$ bfg --delete-files YOUR-FILE-WITH-SENSITIVE-DATA
$ git commit -m "Remove sensitive data"
$ git push --force origin master
```

みたいな感じにすればできる。

##### 参考リンク

- [git filter-branch のかわりに BFG でファイルの履歴を消去する](http://blog.technolize.net/post/82283923159/git-filter-branch-%E3%81%AE%E3%81%8B%E3%82%8F%E3%82%8A%E3%81%AB-bfg-%E3%81%A7%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB%E3%81%AE%E5%B1%A5%E6%AD%B4%E3%82%92%E6%B6%88%E5%8E%BB%E3%81%99%E3%82%8B)


## コマンドを叩いて頑張る方法

意外とシンプルんできるから悪くない。でも長いから覚えるのは無理。  
なんだかんだでBFG入れておいていい気がする。

```
$ git filter-branch -f --index-filter 'git rm --ignore-unmatch YOUR-FILE-WITH-SENSITIVE-DATA' HEAD
```

##### 参考リンク

- [gitで特定のファイルの履歴を消す方法|おし、プログラミング](http://d.hatena.ne.jp/ichhi/20110825/1314300975)
- [GitHub の commit 履歴からパスワードファイル等だけを消す方法](http://qiita.com/MahoTakara/items/3b02bf55767a6372596e)

<hr>

テーマとは関係ないけど、

- `.jar`ファイルはどこに置けばいいのか
- エイリアスを書いておくのは`.zprofile`でいいのか
- エイリアスを作成する以外に良い方法がないのか

というのが気になった。
