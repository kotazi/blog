+++
date = "2016-07-27T11:57:40+09:00"
draft = false
tags = ["CSS"]
title = "【CSS】Android標準ブラウザにおけるPlaceholderのズレ"

+++

iOS/SafariやAndroid/Chromeなどでは再現しなかったが、Androidの標準ブラウザでPlaceholderの位置がずれてしまった。  
調べてみると似たような記事がいくつか見つかった。

- [placeholderを指定したときにAndroidでテキスト表示位置を中央にしたい](http://mmt45.net/2015/02/20/placeholder%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%9F%E3%81%A8%E3%81%8D%E3%81%ABandroid%E3%81%A7%E3%83%86%E3%82%AD%E3%82%B9%E3%83%88%E8%A1%A8%E7%A4%BA%E4%BD%8D%E7%BD%AE%E3%82%92%E4%B8%AD%E5%A4%AE/)
- [Placeholders inputs not centered with Android 4.1.2 #453](https://github.com/topcoat/topcoat/issues/453)

不具合の再現と修正のものを用意した。

<p data-height="300" data-theme-id="15717" data-slug-hash="akKBaa" data-default-tab="html,result" data-user="kotazi" data-embed-version="2" class="codepen">See the Pen <a href="http://codepen.io/kotazi/pen/akKBaa/">akKBaa</a> by Kohei  Saeki (<a href="http://codepen.io/kotazi">@kotazi</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

結局`body`で当てていた`line-height`が良くなかったらしい。
`placeholder`の`height`に合わせて`72px`を当てたら直った。

ちなみに`line-height`の初期値は`normal`。
