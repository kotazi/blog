+++
date = "2016-01-19T13:55:26+09:00"
draft = false
title = "gemをインストールしたけれど見つからない時に疑うこと"
tags = ["git"]
+++

`git-up`をインストールしたけれど使えなかったのでその時の対処法をメモ。

<hr>

###### ◯ `git-up`をインストール

```
$ gem install git-up
```

この状態で`git up`を試したけれど見つからなかった

###### ◯ 探す

```
$ bundle list | grep git-up
```

これは見つかる。

```
$ which git-up
```

これが見つからない。rbenvあたりでパスが通っていないっぽい。

##### ◯ パスを通す

rbev内にgit-upが入っているかを確認する

```
$ find .rbenv -name git-up
.rbenv/versions/2.2.2/bin/git-up
.rbenv/versions/2.2.2/lib/ruby/gems/2.2.0/gems/git-up-0.5.12/bin/git-up
.rbenv/versions/2.2.2/lib/ruby/gems/2.2.0/gems/git-up-0.5.12/lib/git-up
```

あった。

シンボリックリンクを作成する

```
$ ln -s ~/.rbenv/versions/2.2.2/bin/git-up /usr/local/bin
```

最後にgit-upがあることを確認する。

```
$ which git-up
/usr/local/bin/git-up
```
