+++
date = "2016-06-14T11:31:27+09:00"
draft = false
tags = ["git"]
title = "【Git】fork元の更新を追う"

+++

Vuexを最近やっている。  
Cloneしてきたリポジトリを触ったりするわけだけど、
元のリポジトリに変更があれば反映させたい。

その方法を調べました。

<hr>

まずはクローンしたブランチの状態を確認するとこのようになっている。

```
~/W/vuex git:master ❯❯❯ git br -a
* master
  remotes/origin/0.3.0
  remotes/origin/0.4.0
  remotes/origin/HEAD -> origin/master
  remotes/origin/gh-pages
  remotes/origin/master
  remotes/origin/old
```

リモートに`upstream`という名前で本家を追加する。

```
git remote add upstream git@github.com:vuejs/vuex.git
```

フェッチする。

`git fetch upstream`


そして再度ブランチの状態を確認すると、
本家を追従できていることがわかる。

```
~/W/vuex git:master ❯❯❯ git br -a
* master
  remotes/origin/0.3.0
  remotes/origin/0.4.0
  remotes/origin/HEAD -> origin/master
  remotes/origin/gh-pages
  remotes/origin/master
  remotes/origin/old
  remotes/upstream/0.3.0
  remotes/upstream/0.4.0
  remotes/upstream/gh-pages
  remotes/upstream/master
  remotes/upstream/old
```


<hr>

### 本家リポジトリの変更を反映するには

- `git fetch upstream`
- `git merge upstream/master`

となる。

<hr>

[こちらの記事](http://qiita.com/xtetsuji/items/555a1ef19ed21ee42873)を参考にしました。
