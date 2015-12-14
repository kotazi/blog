+++
date = "2015-12-14T16:41:10+09:00"
draft = false
title = "手っ取り早くiPhoneからMacのローカルホストを確認する方法"
tags = ["iPhone", "localhost"]
+++

iPhoneでローカル開発環境をかくにんしたいなと思うけれど、
Charlesを使う方法だったり、ごちゃごちゃさせたり、いろいろ方法がある。
今回はMacとiPhoneで同じWi-Fiに接続できる際に一番手っ取り早い方法。

<hr>

前提条件としてiPhoneとMacが同じWi-Fiに接続されているものとします。

##### ◯ Macのipアドレスを確認する

ターミナル上で`ifconfig`と入力して確認する。

ifconfig内の見方についてはここ。

- [ifconfig　～（IP）ネットワーク環境の確認／設定を行う](http://www.atmarkit.co.jp/ait/articles/0109/29/news004.html)


各インターフェイスの役割については下記が参考になる。

- [ターミナルからプライベートIPアドレスとMACアドレス、ルーティングテーブルを確認する](http://bambinya.hateblo.jp/entry/2015/04/04/234428)

またifconfigでの確認ができなければ、システム環境設定のネットワークから確認が可能。

![](https://www.evernote.com/l/AHWkah5P951L7LCfLBTtPbZVz9miBB-hptcB/image.png)

##### ◯ iPhone側の設定

自分のMacのIPアドレスと確認し、iPhoneのWi-Fi設定を開く。
HTTPプロキシから手動を選択し、MacのIPアドレスを入力する。

![](https://www.evernote.com/l/AHVh_WX90mdKrrXSMLQYw8hVFOwR_lOxmboB/image.jpg)

iPhone側で設定したIPを直接叩けばアクセスできる。
![](https://www.evernote.com/l/AHWUMT8vaWBG2KRS1P_yJj8tahEkoZX4egoB/image.jpg)