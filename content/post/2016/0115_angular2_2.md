+++
date = "2016-01-15T20:07:16+09:00"
draft = true
title = "Angular2のチュートリアル"
tags = ["angular2"]
+++

Angualr2のチュートリアルやったのでメモ。

- [kotazi/angular2-quick-start](https://github.com/kotazi/angular2-quick-start)
- [TUTORIAL: TOUR OF HEROES](https://angular.io/docs/ts/latest/tutorial/)

<hr>

## 構成

```
angular2-tour-of-heroes
|- node_modules
|-app
| |- app.component.ts
| |- boot.ts
| |- hero.ts
| |- hero-detail.component.ts
| |- hero.service.ts
| |- mock-heroes.ts
|- index.html
|- package.json
|- tsconfig.json
```

## index.html

- Angular2では`angular2-polyfills.js`と`Rx.js`が必要
- `System`でconfigとbootの読み込み
- `<my-app>`タグを埋め込む

<script src="https://gist.github.com/kotazi/00d1d0fbe1a4fb92be11.js"></script>

## boot.ts

- `bootstrap()`でアプリケーション起動

<script src="https://gist.github.com/kotazi/bf9b1e29e590582dc895.js"></script>

## app.component.ts

`@Component`はAngularにこのクラスはAngularのコンポーネントだと伝える役割を担う。

`selector`でHTMLのタグ名を指定する。`template`にはレンタリングするHTMLを記述する。

- \*ngFor="#hero of heroes" ng-repeatが変わった
- アスタリスクとシャープが特徴

<script src="https://gist.github.com/kotazi/e407ee5a6dfe7599c8df.js"></script>

## hero.ts

classではなくinterfaceを利用している。  
ここでの選び方は`Hero`をどのように使いたいか。  
ロジックや振る舞いを書く場合は、`class`。
型の定義などだけであれば、`interface`で十分。

<script src="https://gist.github.com/kotazi/958abfee0ee7cce36257.js"></script>

## mock-heroes.ts

- HREOES配列はHero型。

<script src="https://gist.github.com/kotazi/d3b073e5f33ff8ef1605.js"></script>

## hero-detail.component.ts

- [(ngModel)]="hero.name" 書き方変わった

<script src="https://gist.github.com/kotazi/9bd78998321fde979038.js"></script>

## hero.service.ts

<script src="https://gist.github.com/kotazi/f27b3bc4c989d37fde72.js"></script>


<hr>

けっこう変わってるけど抵抗はそんなにないかも。
