+++
date = "2016-03-01T18:04:32+09:00"
draft = false
tags = ["crontab"]
title = "cronによるメールを停止する方法"

+++

cronからのメールが1分おきに来るようになってしまったので直しました。

<hr>

```
crontab -e
```

でcronの設定ファイルを開き、

```
MAILTO=""
```

を追加。以上。

- [定時実行プログラム CRON からのメール通知を無効化する](http://network.station.ez-net.jp/server/linux/service/crond/mail.asp)
