+++
date = "2015-12-18T19:40:35+09:00"
draft = false
title = "クローラとリファラースパム覚書"
+++

404へのアクセスが多いと言われたので調べてみたこと。

<hr>

## クローラのこと

- [クローラーとは／検索エンジンの仕組みをおさらい。クローラー対策とステータスコード|エンジニアのためのSEO入門](http://web-tan.forum.impressrd.jp/e/2009/03/27/4952)

- [クローラーとは？検索エンジンの仕組みを解説！【初心者向け】|ネットビジネスで自由な人生を描くライフスタイルデザイナーノジーのブログ。](http://nozziedesign.com/1341.html)


## リファラースパムのこと

- [リファラースパムとは-スパムURL一覧表（103個）＋対処法](http://think-the-web.com/referrer-spam-measures/)
- [Referer spam - Wikipedia, the free encyclopedia](https://en.wikipedia.org/wiki/Referer_spam)

## その他のこと

Googleアナリティクスの設定からロボットやスパイダーからのヒットを除外できる。

- [Google Analyticsでクローラーのトラフィックを除外する方法|Blogger-customize.com](http://www.blogger-customize.com/2014/08/googleanalytics-crawler.html)


<hr>

リファラスパムの目的はアクセス解析している人間をWebサイトに誘い込むこと。
アクセスを目的としている場合もあれば、アフィサイトに飛ばされることもある。
同時にアクセス解析も正しくできなくなるというデメリットも被ることになる。

スパムクローラーみたいにも呼ばれるのかな？

GoogleAnalyticsでは設定からこれらを除外することができるようになっている。
何故デフォルトでなっていないかといえば、そもそもアクセス解析ができて当初の目的は、
サーバー負荷の計測にあったらしい。そのため、サーバー負荷という観点ではスパムからの
アクセスも同様に計上するべきという考えらしい。 