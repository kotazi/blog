+++
date = "2016-03-16T13:01:21+09:00"
draft = false
tags = ["git"]
title = "【git】There is an existing release branchへの対処"

+++

`git flow`のメモ。

リリースをしようとした時に下記のような注意が出てしまった。

```
~/W/ git:develop ❯❯❯ git flow release start 2016-03-16-0349
There is an existing release branch (2016-03-14-0349). Finish that one first.
```

リリースブランチを作成しようと思ったけれど、もうあるからまずはそれを完了させてくれとのこと。  
webからgithubを確認すると既に`2016-03-14-0349`があるから何でだろうと。


```
~/W/ git:develop ❯❯❯ git branch | grep release
  release/2016-03-14-0349
```

grepしてみるとブランチが存在してる。

```
~/W/ git:develop ❯❯❯ git branch -D release/2016-03-14-0349
Deleted branch release/2016-03-14-0349 (was 0c652fd).
```

ブランチを削除して完了。無事にリリース作業に移れた。
