+++
date = "2016-02-11T13:41:03+09:00"
draft = true
title = "Rails内でのwebpackの使い方"
tags = ["rails", "webpack"]
+++

["How to use webpack with Rails"](http://clarkdave.net/2015/01/how-to-use-webpack-with-rails/)翻訳記事になります。

<hr>


webpackは強力なモジュールバンドラである。主としてフロントエンド開発のために設計されており、
bowerやnpmのJavaScriptモジュールをうまく統合することができる。

Sproketsやアセットパイプラインを用いればシームレスな挿入をすることが可能だけれども、これには典型的なRailsのJSマネジメントシステムを使うによりいくつか大きな利点がある。残念ながらwebpackのドキュメントはまだ要約することが難しく、特にRailsに統合したいとなればなおさらだ。

あなたがまだwebpackを使っていないのであれば、ここに何ができるのかを示そう。

- npmやbowerを使っている全てのフロントエンドのJS(依存関係も)管理する
- CoffeeScriptやES6を自動で処理する
- 最小の力で全てのソースマップを出力する
- 難しい部分でのJSの分離に役に立つ。"common"モジュールが自動的に全てのページに渡ってシェアしてくれる
- 大きなモジュールを複数のファイルに分割し、必要なファイルのみをダウンロードする(`require.ensure`を用いる)

いいなと思ったら、これらを既存のRailsアプリ、もしくは新たなRailsアプリでどのように利用するか呼んでみるといい。
ちなみに、これはRails中心に書かれているけれども、Railsでなくともそのようなフレームワークにwebpackを導入したいのであればきっと役に立つだろう。

<hr>

### Getting Started

#### Is webpack right for your app?

webpackは本当にパワフルで素晴らしいツールである。
けれども本当に効果的に利用するには、あなたが"モジュールとしてのJS"という哲学をしっかりと信じる必要がある。
jQueryやBackboneといった人気のライブラリを使っているなら簡単なことだ。
けれども、特に大きなアプリをwebpackを用いてコンバートしているのなら、いくつかの問題につまづくであろうことを知っておくべきだ。

典型的な問題:

- モジュールにはちゃんと定義されてエントリーポイントがない(webpackはあなたが要求したときに何を含むべきか知らない)
- モジュールに`package.json`や`bower.json`は無効である
- modules which simply stick something on window and call it a day
- modules which add something to jQuery, instead of exporting anything
- modules which, by default, give you a gigantic kitchen sink you don’t need

幸運にもこれらの問題に対処するための様々なメソッドがあるため、webpackで全て解決できる。
さっきも述べたようにwebpackのドキュメントは量が多いので、私が上記についてはカバーする。

あなたのアプリに向いているか？

#### 新しいRailsアプリを作っているところ

大量のJSコードを使うのなら絶対に。試さない理由がない。

#### アプリは大きいがJSはそこまで量がない

多分価値が無いかも。webpackはたくさんのモジュールを使っていたり、たくさんのJSコードを書いているときに
本当に輝く。そんなにJSがないのであれば、大きなメリットはないだろう。

#### アプリは大きいが、JSはよく整備されており、特に問題がない

全てをwebpackに移行することはそれなりに労力がかかるため、多分やる価値はない。

#### アプリは大きく、大量のスパゲッティコード、jsファイルは800kbを超えている

きっとwebpackを使ったほうがいい！少々大変だが、知るべきことはこのガイドで伝える。

始める準備ができたら、Railsアプリをwebpackに移行しよう。


<hr>

### Preparing Rails for webpack

Railsにwebpackを統合するベストプラクティスは存在しないため、多くは意見である。
もしディレクトリの置き場所が気に食わなければ、好きな場所においてもらって構わない。

#### Untangling Sprockets

最初にすることは`app/assets/javascripts`を空にすることだ。
webpackの設定を個々にすると、Sprocketsに拾われてしまう。
全てのJSコードを他の場所に移そう。

`.gitignore`にこれを追加しよう。

```
/app/assets/javascripts
```

これには2つの理由がある。

- 生成されるバンドルは大きくなりがちだったり変更されがちだったりで、ただのgitのスパムを生成することになる
- We’ll be integrating webpack with our deloy process later, which will build a production version of the bundles and placing them here. So, even if you checked in the bundles, you’d be replacing them during deploy anyway.

上記のアドバイスはあなたが全てをwebpackに移行するものとしてのことだ。
もちろんSprocketsのJSバンドラとwebpackを併用することもできる。
その場合は、`.gitignore`を`/app/assets/javascripts/bundle-*`みたいに生成されたJSのみを無視するように修正する。

#### A new home for your JavaScript

`app/assets/javascripts`は生成されたバンドラのためのものなので、実際のJSのために新しいホームを生成する必要がある。
私は新しいフォルダを作成するが、もちろん他の場所においてもいい。

```
app/frontend/javascripts
```

ディレクトリ内部に`entry.js`というファイルを作成しよう。
またあとで説明するので、とりあえず追加してくれ。

```
var _ = require('lodash');
_.times(5, function(i) {
  console.log(i);
});
```

<hr>

### Installing webpack & Bower

#### Installing webpack

webpackはnode.jsのアプリケーションのため、`package.json`がRailsルートの中に必要だ。  
シンプルであるために、webpackとwebpackのモジュールを管理するためだけに使っている。

```
{
  "name": "my-rails-app",
  "description": "my-rails-app",
  "version": "1.0.0",
  "devDependencies": {
    "webpack": "~1.4.13",
    "expose-loader": "~0.6.0",
    "imports-loader": "~0.6.3",
    "exports-loader": "~0.6.2",
    "lodash": "~2.4.1"
  },
  "dependencies": {}
}
```

依存関係に`webpack`があることが見えるだろう。それと多分必要であろういくつかwebpackのローダーを追加した(後で説明する)。

`npm install`をして`node_modules`を確認しよう。
すでにnode.jsはインストール済み？まだだったらインストールしよう！

続いて`webpack`グローバルにインストールし、コマンドラインツールで使えるようにする。

```
$ npm install -g webpack
```

#### Installing Bower (optional)

webpackのいいところのひとつは利用するパッケージ管理ツールを強要しないところだ。
デフォルトはたくさんのフロントエンドモジュールをもったnpmだ。
けれどもBowerというweb用に設計された別のパッケージ管理ツールでしか利用できないフロントエンドライブラリもたくさんある。

jQueryやunderscore、lodashといった人気で整備されているライブラリのみを使う予定なのであればBowerは必要ない。
これらはnpmで十分だ。`package.json`はnpmを使って簡単に追加できる。

しかしながら、もし多くのライブラリを使う必要があったり、単にBowerが好きならセットアップは簡単にできる。
まずは`bower`コマンドをインストールしよう。

```
$ npm install -g bower
```

そしてRailsルートに`bower.json`を作成しよう。

```
{
  "name": "my-rails-app",
  "version": "1.0.0",
  "description": "my-rails-app",
  "dependencies": {
    "jquery": "~1.11.0",
    "lodash": "~2.4.1"
  }
}
```

ここではjQueryとlodashを依存関係に持った最小限の`bower.json`を作成した。
Railsルートで`bower install`を実行すると、bowerはこれらのライブラリを依存関係に沿って`bower_components`にインストールする。

覚えておいて欲しいのが、npmと違いbowerは、フラットな階層で依存関係を解消する。
だからもし、あなたがjQueryの1.x系を指定しながら、別の依存関係では最低のjQueryバージョンを2.x以上と指定していたのであれば、
これを自分自身で解消する必要がある。

### Using bower and npm together

webpack内でnpmとbowerを一緒に使うことを止めるものはいない。
たとえばもしjQueryとBackboneをnpmから、人気のないモジュールをBowerからといったように。
事実、[webpackのドキュメント](https://webpack.github.io/docs/usage-with-bower.html#prefer-modules-from-npm-over-bower)にも書かれている。

In a nutshell, npm (CommonJS-style) modules are typically cleaner and easier for webpack to optimise, which will result in smaller bundles and faster compile times.

In practice, this might not make a huge difference. In the case of large modules like `React`, it may be worth including it as a one-off from npm so webpack can optimise it a little better, but for almost everything else I recommend sticking with Bower so you have one place for all your dependencies.


### Configuring webpack

たくさんの引数を用いて完全にコマンドラインからwebpackを実行することもできるが、複雑になってしまうので、コンフィグファイルを用いて始めよう。

Railsルートに`webpack.config.js`を作成しよう。

```
var path = require('path');
var webpack = require('webpack');

var config = module.exports = {
  // the base path which will be used to resolve entry points
  context: __dirname,
  // the main entry point for our application's frontend JS
  // メインとなるエントリーポイント
  entry: './app/frontend/javascripts/entry.js',
};
```

最終的に非常に複雑なファイルになるので、最小限のところから始めて、説明をしながら加えていこう。
ガイドを通してたくさんのことをフィルについかしていくことになる。
[webpackドキュメント](https://webpack.github.io/docs/configuration.html)にも設定用プロパティの概要については書かれている。

現在ひとつのエントリーファイルを持っているが、配列やオブジェクトを設定することも可能だ。(後に説明)
注意すべき重要な事は、このエントリーファイルがJSのコアとなるということだ。
ここでファイルを指定しなければコンパイルが完了することはない。

次は`output`を追加しよう。コンパイルされたバンドルの場所を占めるものだ。

```
config.output = {
  // this is our app/assets/javascripts directory, which is part of the Sprockets pipeline
  // app/assets/javascriptsディレクトリのこと。Sprocketsの一部となる。
  path: path.join(__dirname, 'app', 'assets', 'javascripts'),
  // the filename of the compiled bundle, e.g. app/assets/javascripts/bundle.js
  // コンパイルされらバンドラのファイル名。
  filename: 'bundle.js',
  // if the webpack code-splitting feature is enabled, this is the path it'll use to download bundles
  // webpackの"code-splitting"機能を使う場合。
  publicPath: '/assets',
};
```

続いて`resolve`を追加する。

```
config.resolve = {
  // tell webpack which extensions to auto search when it resolves modules. With this,
  // you'll be able to do `require('./utils')` instead of `require('./utils.js')`
  // 自動補完をwebpackに伝える。
  // この場合`require('./utils.js')`の代わりに`require('./utils')`とすることができる
  extensions: ['', '.js'],
  // by default, webpack will search in `web_modules` and `node_modules`. Because we're using
  // Bower, we want it to look in there too
  // デフォルトでは`web_module`と`node_modules`を探す。今回はBowerを使うので下記になる
  modulesDirectories: [ 'node_modules', 'bower_components' ],
};
```

最後に`plugins`。

```
config.plugins = [
  // we need this plugin to teach webpack how to find module entry points for bower files,
  // as these may not have a package.json file
  new webpack.ResolverPlugin([
    new webpack.ResolverPlugin.DirectoryDescriptionFilePlugin('.bower.json', ['main'])
  ])
];
```


<hr>

### Running webpack

webpackを実行する前に、Bowerの依存関係をインストールしよう。
NPMだけなら`npm install`を実行すればいい。Bower依存関係をインストールするには:

```
$ bower install
```

`jquery`と`lodash`の入った`bower_components/`ディレクトリがあることだろう。

それが終わればwebpackをRailsルートから実行しよう。

```
$ webpack -d --display-reasons --display-chunks --progress
```

このコマンドはwebpackをdevelopmentモードで実行し、何を実行しているかを表示するようになる。
ゆくゆくはこのコマンドを自動化する。
すべてが上手く行けば、このような出力が確認できるだろう。

```
Hash: cfee07d10692c4ab1eeb
Version: webpack 1.4.14
Time: 548ms
        Asset    Size  Chunks             Chunk Names
    bundle.js  254088       0  [emitted]  main
bundle.js.map  299596       0  [emitted]  main
chunk    {0} bundle.js, bundle.js.map (main) 244421 [rendered]
    [0] ./app/frontend/javascripts/entry.js 73 {0} [built]
     + 2 hidden modules
```

この出力には、`bundle.js`という"チャンク"が作成されたことと、sourcemapが作成されたことが書かれている。
チャンクはJSの分割方法についてだ。
いまはただ、エントリーポイントにつき一つのチャンクを作成しただけだ。
けれどももし、エントリーポイント間にたくさんのモジュールがあったり、code-splitting機能を使っているのであれば、
webpackは`1.1-bundle.js`といった名前でたくさんのチャンクを作成する。

#### The compiled webpack bundle

`app/assets/javascripts/bundle.js`ファイルを開いてみると、コンパイルされたJavaScriptが確認できる。
このファイルでは、標準のJavaScriptは持っていない機能である依存関係の`require`機能を全てのモジュールに提供するwebpackローダーを含んでいる。(数バイト)

webpackが実際に行っていることは下記のコードを見て呼び出しを`require('lodash')`に変えてみるといい。

```
var _ = __webpack_require__(/*! lodash */ 1);
```

全てのモジュールに注入される`__webpack_require__`関数は要求された依存関係をロードすることができる。
サンプル通りにやっているのであれば、50行目は下記のようになっているはずである。

```
/* 0 */
/*!*******************************************!*\
  !*** ./app/frontend/javascripts/entry.js ***!
  \*******************************************/
/***/ function(module, exports, __webpack_require__) {

  var _ = __webpack_require__(/*! lodash */ 1);
  _.times(5, function(i) {
    console.log(i);
  });
```


#### Including webpack bundles in Rails views


思っている通りで、コンパイルされtらJSバンドラは普通に含めることができる。

```
<%= javascript_include_tag 'bundle'  %>
```

基礎はこれで完了。
あとは大きなアプリケーションで必要になりそうなことをカバーしていく。

### Exposing global modules (e.g. jQuery)

特定のモジュールでjQueryを使いたければ下記のように書ける。

```
$ = require('jquery');
$('p').show();
```

けれどもきっとこのようなことがしたいだろう:

- 自動で全てのモジュールにjQueryを使えるようにし、毎回`$ = require('jquery')`のようなことは書く必要がない
- モジュールの外側でも使えるように、`window.$`のようにグローバルオブジェクトにする。

webpackでどちらも可能だ。`ProvidePlugin`を使う。webpackのコンフィグにプラグインの配列を追加する。

```
new webpack.ProvidePlugin({
  $: 'jquery',
  jQuery: 'jquery',
})
```

これで自動的に`$`と`jQuery`を注入するようになり、全てのモジュールに`require`を追加する必要はもはやなくなる。

次のステップとして`window`オブジェクトの追加だ。[loader](https://webpack.github.io/docs/loaders.html)
を追加する。loaders apply some kind of transformation on a file. For example, later we’ll show how to use a loader to transform CoffeeScript files into JavaScript.

`expose`ローダーはモジュールからエクスポートし、それをグローバルに追加する。今回の場合`window`だ。
ou can configure loaders in the webpack config, which makes sense for transformations like CoffeeScript, but you can also specify them when you require a module, which I think makes more sense for the expose loader as it expresses the intent in your code.

`entry.js`のトップに書きを追加する。

```
require('expose?$!expose?jQuery!jquery');
```

この書式がアホらしいことは分かっている。`window.$`と`window.jQuery`にjQueryを追加するために、`expose`ローダーを２回呼び出している。

`require(expose?<libraryName>!<moduleName>)`のように書いている。
`<libraryName>`は`window.libraryName`となり、`<moduleName>`とは含むモジュールのこと。
このケースでは`jquery`だ。`!`を使って上記に書いたようにチェインすることが可能。

さきほどと同じコマンドを使って、再度webpackを実行すると、`$`と`jQuery`にグローバルアクセスできることが分かるだろう。

<hr>

### Source maps

`bundle.js.map `が出力先のディレクトリに作成されていることに気付いただろう。
このソースマップはwebpackの働きによって生成されている。
The source maps generated by webpack work extremely well. You get to download a single bundle (instead of 10+ individual files, which can get slow) but can view errors inside individual files, as they exist on your file system. And of course, if you’re using CoffeeScript and friends, you can view errors in the context of the actual CoffeeScript file.

However, by default Sprockets will break the source maps by appending a semi-colon to them, so browsers can’t parse them. You can fix this with the following configuration option:

```
Rails.application.config.assets.configure do |env|
  env.unregister_postprocessor 'application/javascript', Sprockets::SafetyColons
end
```

At this to your `config/initializers/assets.rb` (or directly in `config/application.rb` for older versions of Rails). Then clear your Sprockets cache: $ rm -r tmp/cache`

Now when you get errors, or view loaded sources in a browser, you’ll see the actual file (e.g. entry.js) instead of the giant bundled file.

#### Virtual source paths

In Chrome, by default the source map generated by webpack will put everything in a 'pseudo path’, `webpack://`, when you view it in the inspector’s Sources tab. You can make this a bit nicer by adding the following to your webpack `config.output`:

```
devtoolModuleFilenameTemplate: '[resourcePath]',
devtoolFallbackModuleFilenameTemplate: '[resourcePath]?[hash]',
```

Now your 'virtual’ source files will appear under the `domain > assets` directory in the Sources tab.

#### Sprockets cache and source maps

In my experience, Sprockets can be very aggressive at caching source maps. If they ever start acting weird, make sure to clear the sprockets cache in `tmp/cache` first.

<hr>

### Loading CoffeeScript and other transpiled languages

CoffeeScriptやその類で書かれたモジュールをローダーを使って自動でトランスパイルすることができる。
`expose`ローダーのように、`require`ステートメントの内部で実行されるが、webpackのコンフィグに記述することがよいとは言えないので、
CoffeeScriptモジュールをrequireする。

はじめに、書きのように`package.json`に`coffee-loader`をインストールする。

```
$ npm install coffee-loader@0.7.2 --save-dev
```

webpackのコンフィグで、`config.resolve.extensions`も書いておく。

```
extensions: ['', '.js', '.coffee']
```

最後に、ローダーを追加する。

```
config.module = {
  loaders: [
    { test: /\.coffee$/, loader: 'coffee-loader' },
  ],
};
```

`app/frontend/javascripts/app.coffee`を作成する。

```
_ = require('lodash')

module.exports = class App
  start: ->
    _.times 3, (i) -> console.log(i)
```

既存の`entry.js`を更新する。

```
require('expose?$!expose?jQuery!jquery');
var App = require('./app');

var app = new App();
app.start();
```

再度webpackを実行して、ブラウザで予想通りに動作していることが確認できる。

### Code splitting and lazily loading modules
