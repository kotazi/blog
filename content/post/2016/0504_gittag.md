+++
date = "2016-05-04T11:45:41+09:00"
draft = false
tags = ['git']
title = "【git】タグを間違えてしまった"

+++


gitのタグを修正することがあったのでメモ書き。

注釈を間違えてしまったのでやりなおす方法。

<hr>

- ローカルのタグを削除

```
git tag -d TAG_NAME
```

- リモートのタグを削除(空のタグで上書き)

```
git push origin :refs/tags/TAG_NAME
```

- 新しくタグをつける(注釈付き)

```
git tag TAG_NAME
```

- リモートのプッシュ

```
git push --tags origin
```

<hr>

意外とすんなり。
