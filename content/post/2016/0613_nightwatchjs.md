+++
date = "2016-06-13T17:17:08+09:00"
draft = false
tags = ["E2E", "Nightwatch"]
title = "【サンプルあり】Nightwatch.js覚書"

+++

[Nightwatch.js](http://nightwatchjs.org/)について簡単に学び、簡単にまとめました。

<hr>

## 概要

### Nightwatchとは？

- [Selenium WebDriver API](http://code.google.com/p/selenium/wiki/JsonWireProtocol)を使い、[Node.js](http://nodejs.org/)で書かれたWebアプリケーション/Webサイト用の自動テストフレームワーク
- 継続的インテグレーションを行うためのE2Eテスト自動化へのソリューション

### Seleniumの概要

- ブラウザ自動制御のための人気で包括できなツール。元々Javaだったが現在は多くの言語をサポート
- Nightwatchはブラウザ自動制御のためにSelenium WebDriver(特に[WebDriver Wire Protocol](http://code.google.com/p/selenium/wiki/JsonWireProtocol))を使っている

### オペレーションについて

NightwatchはSeleniumサーバーに正しいパラメータを付与してHTTPリクエストを送ることで動作する。  
RESTfulなAPIプロトコルは[Selenium JsonWireProtocol](http://code.google.com/p/selenium/wiki/JsonWireProtocol#/session)に定義されている。

![http://nightwatchjs.org/img/operation.png](http://nightwatchjs.org/img/operation.png)

Nightwatchでは多くの場合、Seleniumサーバーに対して少なくとも2回のリクエストを送る必要がある。最初のリクエストではCSSセレクタで与えられた要素を見つけるためのものであり、次のリクエストでコマンドに対して与えられた要素を演算する。


## サンプル(公式から)

CSSセレクタを使うことで、簡単にE2Eテストを書ける。  


```
module.exports = {
  'Demo test Google' : function (browser) {
    browser
      // URLを指定
      .url('http://www.google.com')
      // bodyタグが表示されるまで1000ms待つ
      .waitForElementVisible('body', 1000)
      // input[type=text]に"nightwatch"とセット
      .setValue('input[type=text]', 'nightwatch')
      // button[name=btnG]が表示されるまで1000ms待つ
      .waitForElementVisible('button[name=btnG]', 1000)
      // button[name=btnG]ボタンをクリック
      .click('button[name=btnG]')
      // 1000ms待つ
      .pause(1000)
      // #mainタグがNight Watchを含んでいることを確認する
      .assert.containsText('#main', 'Night Watch')
      // 終了
      .end();
  }
};
```

必要に応じて複数のステップに分割することも可能。

```
module.exports = {
  'step one' : function (browser) {
    browser
      .url('http://www.google.com')
      .waitForElementVisible('body', 1000)
      .setValue('input[type=text]', 'nightwatch')
      .waitForElementVisible('button[name=btnG]', 1000)
  },

  'step two' : function (browser) {
    browser
      .click('button[name=btnG]')
      .pause(1000)
      .assert.containsText('#main', 'Night Watch')
      .end();
  }
};
```


### 書いてみる

Nightwatchで[Vue.js • TodoMVC](http://todomvc.com/examples/vue)のE2Eテストを書いてみた。

- サイト表示
- タスクの追加
- タスクの完了

をテストする。

```
module.exports = {
  // TodoMVCサイトにアクセスするテスト
  'Access to TodoMVC' : function (browser) {
    browser
      .url('http://todomvc.com/examples/vue/')
      .waitForElementVisible('body', 1000);
  },

  // Todoを追加するテスト
  'Add a Todo task': function (browser) {
    browser
      .setValue('.new-todo', ['nightwatch testing', browser.Keys.ENTER])
      .pause(1000)
      .assert.containsText('.todo-list', 'nightwatch testing')
  },

  // Todoを完了するテスト
  'Complate the Task': function (browser) {
    browser
      .click('input.toggle[type=checkbox]')
      .click('.filters li:last-child')
      .pause(1000)
      .assert.containsText('.todo-list', 'nightwatch testing')
      .end();
  }
};

```

<img src="https://www.evernote.com/l/Aj0ZHurS-ElLx4ysBBbKhNlEdlaM1BJ33x8B/image.png" alt="Pasted%20Image%206/13/16,%2018:14" />

![](http://g.recordit.co/AqTNXin4mr.gif)
