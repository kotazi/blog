+++
date = "2016-02-08T18:45:53+09:00"
draft = false
title = "0202_angular_unit"
tags = ["Angular", "unit", "Jasmine"]
+++

- [Unit Testing|AngularJS]

<hr>

JavaScriptは強い式の力を持った動的型付け言語であり、コンパイラの助けを得ることがない。
そのため、JavaScriptのコードにはしっかりしたテストが必要である。
Angularには、Angularアプリケーションのテストを用意にするための機能がたくさん組み込まれている。
Angularにおいて、テストをしないということはありえない。

<hr>

### Separation of Concerns

ユニットテストという名前から示されるように、これは独立した単体のコードのテストのことである。
ユニットテストは「そのロジックは正しく動くだろうか」、「ソート関数が並び替えたリストは正しい順番だろうか」
といった質問に回答しようとするものだ。

テストにおいてこのような質問に答えるためには、単体のコードを孤立させることがとても重要である。
ソート関数のテストをしている時に、関連するDOM要素を生成したり、ソートデータをフェッチするためにXHRを呼び出すといったことをしたくないため。

これは明らかなことなのに、典型的なプロジェクトでは独立した関数を呼び出すことはとてもむずかしい。
これは開発者がしばしば全てのことをするコードを混合させてしまうことに原因があり、
XHRを生成するもの、受け取ったデータをソートするもの、そしてDOMを操作するもの、である。

Angularを用いて、あなたが正しくことを行うことを簡単にした。
XHRのために依存性注入を提供するので、リクエストをシミュレーションすることが可能になり、
レスポンスデータをソートすることができ、DOMを操作することができる。
DOMを生成したり、状態を監視したりXHRリクエストのレスポンスデータを待ったりすることなく、
データのソートをテストすることができる。
単独のソート関数を分離してテストすることができる。

<hr>

### With great power comes great responsibility

Angularはテストしやすいことを念頭に書かれているが、それでもまだ
正しい書き方をする必要がある。
書き方が簡単になるよう努めたものの、ガイドラインを無視すれば
アンテスタブルなアプリケーションになってしまう。

<hr>

### Dependency Injection

Angularは[依存性注入](https://docs.angularjs.org/guide/di)のビルトインからなっており、
それはテストのコンポーネント化するため、思いどおりにコンポーネント間での依存関係やスタブ、モックをテストすることが可能。

コンポーネントに依存性注入をすればテストのモックが簡単になり、
グローバル汚染もなくなりうっかり別のテストに影響してしまうこともなくなる。

<hr>

### Additional tools for testing Angular applications

Angularアプリケーションのテストでは、テストをより簡単にセットアップし実行するために使うべきツールがある。

#### Karma

[Karma](http://karma-runner.github.io/)はコマンドラインツールである。
アプリケーションのコードを読み込み、実行するWebサーバーを用意する。
アプリケーションがサポートする全てのブラウザにで操作することをテストするために、ブラウザの数分に対してKarmaを実行するように設定することができる。
Karmaをコマンドライン上で実行すると、ブラウザでのテスト実行結果をコマンドライン上にすぐに表示する。

KarmaはNodeJS製のアプリケーションであり、npmでインストールする。
インストールガイドについては[Karmaのサイト](http://karma-runner.github.io/0.12/intro/installation.html)が役に立つ。

#### Jasmine

[Jasmin](http://jasmine.github.io/1.3/introduction.html)はビヘイビア駆動開発のJavaScriptフレームワークであり、
Angularアプリケーションをテストするうえで最も人気のフレームワークである。
テストに構造をもたせ、アサーションを作成するための関数を提供する。
テストが大きくなっても構造的であり、ドキュメント性を保ちながら、アーカイブしてく。

テストをグループ化するためには`describe`を利用する。

```
describe("sorting the list of users", function() {
  // individual tests go here
});
```

個々のテストは`it`関数の中に定義される。

```
describe('sorting the list of users', function() {
  it('sorts in descending order by default', function() {
    // your test assertion goes here
  });
});
```

関連するテストを`describe`ブロックでグルーピングし、個々のテストは`it`関数内に記述することで
テストをドキュメントとして保ち続けることが可能になる。

最後に、Jasmineは作成するアサーションのマッチャーを提供する。

```
describe('sorting the list of users', function() {
  it('sorts in descending order by default', function() {
    var users = ['jack', 'igor', 'jeff'];
    var sorted = sortUsers(users);
    expect(sorted).toEqual(['jeff', 'jack', 'igor']);
  });
});
```

Jasminは様々なアサーションを作成するためのたくさんのマッチャーから成っている。
マッチャーについては[こちらの記事](http://jasmine.github.io/1.3/introduction.html#section-Matchers)を読むこと。
KarmaとJasminを使うために、[karma-jasmin](https://github.com/karma-runner/karma-jasmine)を利用する。

#### angular-mocks

Angularはテスト用のモックを提供する`ngMock`というものを用意している。
ユニットテスト内でのAngularサービスをモックしたりインジェクトするために用いられる。
加えて、別のモジュールを拡張できるので、同期的だ。
テストを同期することで明確に保ち、簡単に動作させることができる。
もっとも便利なうちのひとつが`$httpBackend`だ。
テストでXHRリクエストのモックを行うことができ、代わりにサンプルのデータを返す。

<hr>

### Testing a Controller

Angularのビューレイヤーはロジックから切り離されているので、コントローラを簡単にテストすることができる。
下記でコントローラをどのようにテストしているか見ていこう。
パスワードの長さに基づいてスコープのプロパティをセットする`$scope.grade`を提供している。

```
angular.module('app', [])
.controller('PasswordController', function PasswordController($scope) {
  $scope.password = '';
  $scope.grade = function() {
    var size = $scope.password.length;
    if (size > 8) {
      $scope.strength = 'strong';
    } else if (size > 3) {
      $scope.strength = 'medium';
    } else {
      $scope.strength = 'weak';
    }
  };
});
```

コントローラはグローバルスコープでは利用できないため、
はじめに`angular.mock.inject`を使ってコントローラをインジェクトする必要がある。
最初のステップは、angular-mocksが提供する`module`関数を使うことだ。
この関数は与えられたモジュールを読み込み、読み込まれたモジュールはテスト内で利用う可能となる。
これをJasminの提供する関数であり、各テストの前にコードを実行できる`beforeEach`に渡す。
そしてコントローラのインスタンス化に責任を持つサービス`$controller`にアクセスするために`inject`を用いる。

```
describe('PasswordController', function() {
  beforeEach(module('app'));

  var $controller;

  beforeEach(inject(function(_$controller_){
    // The injector unwraps the underscores (_) from around the parameter names when matching
    $controller = _$controller_;
  }));

  describe('$scope.grade', function() {
    it('sets the strength to "strong" if the password length is >8 chars', function() {
      var $scope = {};
      var controller = $controller('PasswordController', { $scope: $scope });
      $scope.password = 'longerthaneightchars';
      $scope.grade();
      expect($scope.strength).toEqual('strong');
    });
  });
});
```

ネストした`describe`の呼び出しや、文字列を用いて呼び出す時の描写でテストが明確になっていることに注目する。
何をテストしているかハッキリと記し、何が起きているかを簡単に理解できる。
では3文字以下のパスワードを追加して、`$scope.strength`が"weak"であるか見ていこう。

```
describe('PasswordController', function() {
  beforeEach(module('app'));

  var $controller;

  beforeEach(inject(function(_$controller_){
    // The injector unwraps the underscores (_) from around the parameter names when matching
    $controller = _$controller_;
  }));

  describe('$scope.grade', function() {
    it('sets the strength to "strong" if the password length is >8 chars', function() {
      var $scope = {};
      var controller = $controller('PasswordController', { $scope: $scope });
      $scope.password = 'longerthaneightchars';
      $scope.grade();
      expect($scope.strength).toEqual('strong');
    });

    it('sets the strength to "weak" if the password length <3 chars', function() {
      var $scope = {};
      var controller = $controller('PasswordController', { $scope: $scope });
      $scope.password = 'a';
      $scope.grade();
      expect($scope.strength).toEqual('weak');
    });
  });
});
```

現在２つのテストがあるが、テストが重複していることに気付いて欲しい。
両方で`$scope`を作成しており、コントローラを生成している。
テストを重複するならこの重複は良くない。
ありがたいことにJasminには`beforeEach`がある。
個々のテストの前に関数を実行してくれる。
どのようにテストをセットするか確認しよう。

```
describe('PasswordController', function() {
  beforeEach(module('app'));

  var $controller;

  beforeEach(inject(function(_$controller_){
    // The injector unwraps the underscores (_) from around the parameter names when matching
    $controller = _$controller_;
  }));

  describe('$scope.grade', function() {
    var $scope, controller;

    beforeEach(function() {
      $scope = {};
      controller = $controller('PasswordController', { $scope: $scope });
    });

    it('sets the strength to "strong" if the password length is >8 chars', function() {
      $scope.password = 'longerthaneightchars';
      $scope.grade();
      expect($scope.strength).toEqual('strong');
    });

    it('sets the strength to "weak" if the password length <3 chars', function() {
      $scope.password = 'a';
      $scope.grade();
      expect($scope.strength).toEqual('weak');
    });
  });
});
```

重複部分を外へ出し、`beforeEach`ブロックへ移動した。
これで個々のテストには特有のコードだけになり、全体に入っているようなコードはなくなった。
テストを拡張するうえで、テストをきちんとするためにどこで`beforeEach`を使えるか目を見張ることだ。
Jasminが提供するのは`beforeEach`だけじゃない。[こちら](http://jasmine.github.io/1.3/introduction.html#section-Setup_and_Teardown)も見てみること。

<hr>

### Testing Filters

[フィルタ](https://docs.angularjs.org/api/ng/provider/$filterProvider)はデータをユーザが読みやすいフォーマットに変換する。
これはフォーマットの責任をアプリケーションロジックから取り除くことになるのでとても重要なこと。さらにはアプリケーションロジックを単純にできる。

```
myModule.filter('length', function() {
  return function(text) {
    return ('' + (text || '')).length;
  }
});

describe('length filter', function() {

  var $filter;

  beforeEach(inject(function(_$filter_){
    $filter = _$filter_;
  }));

  it('returns 0 when given null', function() {
    var length = $filter('length');
    expect(length(null)).toEqual(0);
  });

  it('returns the correct value when given a string of chars', function() {
    var length = $filter('length');
    expect(length('abc')).toEqual(3);
  });
});
```

<hr>

### Testing Directives

AngularのディレクティブはカスタムHTMLタグや属性、クラス、コメントと複雑な機能に責任を持っている。
ディレクティブを使って生成したコンポーネントがさまざまな背景で思った通りに使われているかのテストになるので、
ディレクティブのユニットテストはとても重要です。

#### Simple HTML Element Directive

依存関係のないAngularアプリで始める。

```
var app = angular.module('myApp', []);
```

アプリにディレクティブを追加する。

```
app.directive('aGreatEye', function () {
    return {
        restrict: 'E',
        replace: true,
        template: '<h1>lidless, wreathed in flame, {{1 + 1}} times</h1>'
    };
});
```

`<a-great-eye></a-great-eye>`といったタグでこのディレクティブは利用する。
タグごと`<h1>lidless, wreathed in flame, {{1 + 1}} times</h1>`テンプレートを置き換えるものだ。
その機能を確認するためのJasminテストを書いていく。
レンダリングされると`{{1 + 1}}`式が評価されることに注意する。

```
describe('Unit testing great quotes', function() {
  var $compile,
      $rootScope;

  // ディレクティブを含んだmyAppモジュールを読み込む
  beforeEach(module('myApp'));

  // $rootScopeと$compileへの参照を保持する
  // このdescribeブロックにおいて全てのテストで利用可能になる
  beforeEach(inject(function(_$compile_, _$rootScope_){
    // The injector unwraps the underscores (_) from around the parameter names when matching
    $compile = _$compile_;
    $rootScope = _$rootScope_;
  }));

  it('Replaces the element with the appropriate content', function() {
    // ディレクティブのHTMLをコンパイルする
    var element = $compile("<a-great-eye></a-great-eye>")($rootScope);
    // ウォッチャーを発火させ、式を評価する
    $rootScope.$digest();
    // コンパイルされた要素がテンプレートの中身を含んでいることを確認する
    expect(element.html()).toContain("lidless, wreathed in flame, 2 times");
  });
});
```

`$compile`サービスと`$rootScope`をJasminテストの前にインジェクトする。
`$compile`サービスはaGreatEyeディレクティブをレンダリングするために利用する。
レンダリング後に、"lidless, wreathed in flame, 2 times"は現れる。

**Underscore notation:** `_$rootScope_`といったアンダースコアの使い方は、テスト内での変数名をきれいに保つためAngularJSコミュニティ内で広く広がった風習である。
パラメータがマッチングした際に、`$injector`がアンダースコアを取り払うのはこれが理由である。
このアンダースコアルールが働くのは名前が一つのアンダースコアで始まり一つのアンダースコアで終わる時のみに限られ、それ以外での置換は起こらない。

#### Testing Transclusion Directives

トランスクルージョンを使ったディレクティブはコンパイラに特別に扱われる。
コンパイル関数が呼び出される前に、ディレクティブ要素のコンテンツは取り除かれ、トランスクルージョン関数を通して提供される。
ディレクティブのテンプレートはその際に、ディレクティブ要素に追加され、トランスクルードされたコンテンツをテンプレート内に挿入する。

コンピレーション前:

```
<div transclude-directive>
  Some transcluded content
</div>
```

トランスクルージョンが引き抜かれる:

```
<div transclude-directive></div>
```

コンピレーション後:

```
<div transclude-directive>
  Some Template
  <span ng-transclude>Some transcluded content</span>
</div>
```

ディレクティブが"要素"のトランスクルージョンを利用しているのであれば、コンパイラは実質的にDOMからディレクティブ全体の要素を取り除き、
コメントノードを持ってそれを置き換える。
そしてコンパイラはディレクティブのテンプレートをコメントノードの**後に**、兄弟として挿入する。


コンピレーション前:

```
<div element-transclude>
  Some Content
</div>
```

トランスクルージョンが引き抜かれる:

```
<!-- elementTransclude -->
```

コンピレーション後:

```
<!-- elementTransclude -->
<div element-transclude>
  Some Template
  <span ng-transclude>Some transcluded content</span>
</div>
```

要素のトランスクルージョンを使ったディレクティブのテストを書いている時に、このことに気づくことが重要。
もし`$compile`へ渡すDOMフラグメントのルート要素にディレクティブを置くのであれば、関連する関数から返されるDOMノードはコメントノードであり、テンプレートとトランスクルードされたコンテンツを繋ぐ能力には負けるだろう。

```
var node = $compile('<div element-transclude></div>')($rootScope);
expect(node[0].nodeType).toEqual(node.COMMENT_NODE);
expect(node[1]).toBeUndefined();
```

これに対処するためには単純に要素をトランスクルードするディレクティブを`<div>`のようなエレメントでラップする。

```
var node = $compile('<div><div element-transclude></div></div>')($rootScope);
var contents = node.contents();
expect(contents[0].nodeType).toEqual(node.COMMENT_NODE);
expect(contents[1].nodeType).toEqual(node.ELEMENT_NODE);
```

#### Testing Directives With External Templates

もぢディレクティブで`templateUrl`を使っているのであれば、HTMLテンプレートをプリコンパイルするために[karma-ng-html2js-preprocessor](https://github.com/karma-runner/karma-ng-html2js-preprocessor)を使うことを考え、HTTPリクエストを使ってテキストをロードすることは避けること。
