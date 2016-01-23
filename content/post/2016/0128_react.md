+++
date = "2016-01-22T17:08:28+09:00"
draft = true
title = "【訳読】React Tutorial"
tags = "react"
+++

[Reactチュートリアル](https://facebook.github.io/react/docs/tutorial.html).

<hr>

## Tutorial

シンプルではあるものの、ブログに書ける現実的なコメントボックスを実装していく。
この基本バージョンのリアルタイムコメントはDisqusやLibeFyre、Facebook commentsにオファーをいただいた。

提供する機能:

- 全てのコメントのView
- コメントを送信するためのフォーム
- バックエンドをカスタムするためのHook

<br>

またこれらの機能もしっかり備えている。

- **オプティミスティックコメント**: サーバーに保存される前にコメントが表示されるため非常に速い
- **ライブアップデート**: 他者のコメントがリアルタイムで反映される
- **Markdown対応**: Markdownによる記述が可能

<hr>

### Want to skip all this and just see the source?

- [GitHub](https://github.com/reactjs/react-tutorial)

<hr>

### Running a server

チュートリアルを開始するためには、稼働しているサーバーが必要。
あくまでデータの取得と保存に使用するAPIエンドポイントとしてのサーバーだ。

できるだけ簡単にするため、仕様を実現するシンプルなサーバーをいくつかのスクリプト言語で用意した。
開始に必要なものは[こちら](https://github.com/reactjs/react-tutorial/)からインストール可能。

簡潔にするために、実行するサーバーはJSONファイルをデータベースとして利用する。
本番でこんなことはしないが、APIを用いたシュミレーションを行うには簡単でいいだろう。
一度サーバーを起動したら、APIエンドポイントをサポートし、必要なスタティックペーシを出してくれる。

<hr>

### Getting started

このチュートリアルでは、できるだけ簡単なものにしていくつもりである。
サーバーパッケージの中に私たちが動かすHTMLファイルが含まれている。
`public/index.html`を好みのエディタで開いてみてほしい。このようになっているはずだ。

```
<!-- index.html -->
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>React Tutorial</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/react/0.14.6/react.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/react/0.14.6/react-dom.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/babel-core/5.8.23/browser.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/2.1.1/jquery.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/marked/0.3.2/marked.min.js"></script>
  </head>
  <body>
    <div id="content"></div>
    <script type="text/babel" src="scripts/example.js"></script>
    <script type="text/babel">
      // To get started with this tutorial running your own code, simply remove
      // the script tag loading scripts/example.js and start writing code here.
    </script>
  </body>
</html>
```

このチュートリアルのリマインダーとして、このスクリプトタグの中にコードを記述している。
ライブリロードは備えていないので、ファイルを更新した際はブラウザをリロードすること。
(サーバー起動後、)ブラウザで`http://localhost:3000`を開けば進捗が確認できる。
このファイルをそのまま開けば、完成したプロダクトを確認することができる。
準備ができたら`<script>`タグを削除して続けよう。

注意: ajax呼び出しをシンプルに記述するため、jQueryを含んでいるが必ずしも必要なわけではない。

<hr>

### Your first component

Reactは要するにmodular, composable componentsである。
コメントボックスのサンプルとして、次のようなコンポーネント構造を持つことにする。

```
- CommentBox
  - CommentList
    - Comment
  - CommentForm
```

シンプルに`<div>`を使って、`CommentBox`コンポーネントを作成しよう。

```
// tutorial1.js
var CommentBox = React.createClass({
  render: function() {
    return (
      <div className="commentBox">
        Hello, world! I am a CommentBox.
      </div>
    );
  }
});
ReactDOM.render(
  <CommentBox />,
  document.getElementById('content')
);
```

ネイティブのHTMLエレメント名が小文字で綴られていることに注意。
Reactクラスは大文字で始まっている。

##### JSX Syntax

JSがXMLのような構文だったことに気づかれただろう。
シンタックスシュガーをピュアJSに変換するシンプルなプリコンパイラを備えている。

```
// tutorial1-raw.js
var CommentBox = React.createClass({displayName: 'CommentBox',
  render: function() {
    return (
      React.createElement('div', {className: "commentBox"},
        "Hello, world! I am a CommentBox."
      )
    );
  }
});
ReactDOM.render(
  React.createElement(CommentBox, null),
  document.getElementById('content')
);
```

任意ではあるがJSXシンタックスを使ったほうがかんだんだ。
JSXについては[こちら](https://facebook.github.io/react/docs/jsx-in-depth.html)

##### What's going on

新しいReactコンポーネントを作成するために、`React.createClass()`にいくつかのメソッドを持ったJSオブジェクトを渡した。
これらのメソッドの中で最も重要なものが`render`メソッドだ。
Reactコンポーネントのツリー構造を返り値にし、ゆくゆくはHTMLにレンダリングするメソッドだ。

`<div>`タグは実際のDOMノードではない。Reactの`div`コンポーネントのインスタンスである。
これらのことはReactの操作方法を知るデータの欠片や印と思ってくれていい。
Reactは安全だ。
HTML文字列を生成しないため、XSS対策をデフォルトでしていることになる。

基本的なHTMLを返す必要はない。あなた(もしくは誰か)の組み立てたコンポーネントのツリーを返せばいい。
これがReactをcomposableにしてくれる。フロントエンドを保守するキーとなる。

`ReactDOM.render()`はルートコンポーネントをインスタンス化し、骨組みを作る。
そしてマークアップ部分を生のDOMへ注入し、ふたつ目の引数として提供される。

`ReactDOM`モジュールはDOM特有のメソッドを提供する。
`React`は[React Native](http://facebook.github.io/react-native/)といったことなるプラットフォーム上で共有されるコアのツールを持つ。
`ReactDOM.render`がこのチュートリアルのスクリプトの最下部にとどまることは重要な事だ。
`ReactDOM.render`は複合的なコンポーネントが定義された後に呼び出されるべきだからである。

<hr>

### Composing components

それでは再びシンプルな`<div>`を使って`CommentList`と`CommentForm`の骨組みを作成しよう。
ファイルに２つのコンポーネントを作成し、`CommentBox`宣言を保ったうえで`ReactDOM.render`を呼び出す。

```
// tutorial2.js
var CommentList = React.createClass({
  render: function() {
    return (
      <div className="commentList">
        Hello, world! I am a CommentList.
      </div>
    );
  }
});

var CommentForm = React.createClass({
  render: function() {
    return (
      <div className="commentForm">
        Hello, world! I am a CommentForm.
      </div>
    );
  }
});
```

続いて`CommentBox`コンポーネントを更新し、新しいコンポーネントを利用する。

```
// tutorial3.js
var CommentBox = React.createClass({
  render: function() {
    return (
      <div className="commentBox">
        <h1>Comments</h1>
        <CommentList />
        <CommentForm />
      </div>
    );
  }
});
```

どのようにしてHTMLタグと作成したコンポーネントを混在させるかに注意すること。
HTMLコンポーネントは通常Reactコンポーネントであり、ひとつの違いはあるが、あなたが定義したもののようだ。
JSXコンパイラは自動的にHTMLタグを`React.createElement(tagName)`式に書き直し、全てを孤独にする。
これが名前名前汚染を防ぐことになる。

#### Using props

`Comment`コンポーネントを作成しよう。親から渡されたデータに依存するものになる。
親コンポーネントから渡されたデータは子コンポーネントの`property`として利用可能だ。
これらのプロパティには`this.props`を使ってアクセスする。`props`を使えば、`CommentList`から渡された`Comment`を読むことができ、レンダリングも可能だ。

```
// tutorial4.js
var Comment = React.createClass({
  render: function() {
    return (
      <div className="comment">
        <h2 className="commentAuthor">
          {this.props.author}
        </h2>
        {this.props.children}
      </div>
    );
  }
});
```

JSXの中のJavaScript式を取り囲むことで、ツリー内にReactコンポーネントを書くことができる。
コンポーネントの属性やネストされた要素に`this.props`や`this.props.children`を用いてアクセスする。

#### Component Properties

`Comment`コンポーネントの定義が完了したところで、コンポーネントに名前とコメント分を渡していきたい。
各ユニークなコメントに対して同じコードの再利用が可能になる。
`CommentList`にコメントを追加してみよう。

```
// tutorial5.js
var CommentList = React.createClass({
  render: function() {
    return (
      <div className="commentList">
        <Comment author="Pete Hunt">This is one comment</Comment>
        <Comment author="Jordan Walke">This is *another* comment</Comment>
      </div>
    );
  }
});
```

親の`CommentList`コンポーネントから子の`Comment`コンポーネントにデータを渡していることに注目。
たとえば、属性をお通して"Pete Hunt"、XMLのような子ノードで"This is one comment"を渡している。
さらに、`Comment`コンポーネントはこれらのプロパティに`this.props.author`や`this.props.children`を通してアクセスしている。

#### Adding Markdown

Markdownはテキストをシンプルに記述するためのフォーマットだ。
例えばテキストをアスタリスクで囲めば、テキストを強調させる。

このチュートリアルでは、受け取ったMarkdownを生のHTMLにコンバートするサードパーティライブラリである*marked*を利用する。既にこのライブラリは読み込んであるので、もう使いはじめることができる。コメント文のMarkdownをコンバートして出力しよう。

```
// tutorial6.js
var Comment = React.createClass({
  render: function() {
    return (
      <div className="comment">
        <h2 className="commentAuthor">
          {this.props.author}
        </h2>
        {marked(this.props.children.toString())}
      </div>
    );
  }
});
```

ここではmarkedライブラリの呼び出しを行っているだけだ。`this.props.children`をReactのラッパーテキストからmarkedが理解できるように生の文字列にコンバートする必要があるため`toString()`を呼び出している。

けれども問題がひとつ！レンダリングされたコメントが`<p>This is <em>another</em> comment</p>`のようになっている。
タグを実際のHTMLとしてレンダリングしたいのに。

これは[XSS攻撃](https://en.wikipedia.org/wiki/Cross-site_scripting)からの防御手段となる。

```
// tutorial7.js
var Comment = React.createClass({
  rawMarkup: function() {
    var rawMarkup = marked(this.props.children.toString(), {sanitize: true});
    return { __html: rawMarkup };
  },

  render: function() {
    return (
      <div className="comment">
        <h2 className="commentAuthor">
          {this.props.author}
        </h2>
        <span dangerouslySetInnerHTML={this.rawMarkup()} />
      </div>
    );
  }
});
```
特別なAPIを使って故意に生HTMLの挿入を難しくしているが、markedにとってはメリットとなる。

こうすることでmarkedの安全性に信頼を置くことができる。このケースでは、`sanitize: true`を渡してmarkedにそのまま渡すのではなくソース内のどんなHTMLでもエスケープするように伝えた。

#### Hook up the data model

これまで私たちはコメントをソースコード内に直接挿入してきた。
代わりにJSONデータの塊をコメントリストにレンダリングしてみよう。
ゆくゆくはサーバーから受け取るデータだが、ここではコード内に記述しよう。

```
// tutorial8.js
var data = [
  {id: 1, author: "Pete Hunt", text: "This is one comment"},
  {id: 2, author: "Jordan Walke", text: "This is *another* comment"}
];
```

データをモジュール方式に`CommentList`に入れる必要がある。
`CommentBox`を修正し、このデータをプロパティを通して`CommentList`に入れるために`ReactDom.render()`を呼び出す。

```
// tutorial9.js
var CommentBox = React.createClass({
  render: function() {
    return (
      <div className="commentBox">
        <h1>Comments</h1>
        <CommentList data={this.props.data} />
        <CommentForm />
      </div>
    );
  }
});

ReactDOM.render(
  <CommentBox data={data} />,
  document.getElementById('content')
);
```

これでデータが`CommentList`内で利用可能になる、コメントをレンダリングしよう。

```
// tutorial10.js
var CommentList = React.createClass({
  render: function() {
    var commentNodes = this.props.data.map(function(comment) {
      return (
        <Comment author={comment.author} key={comment.id}>
          {comment.text}
        </Comment>
      );
    });
    return (
      <div className="commentList">
        {commentNodes}
      </div>
    );
  }
});
```

#### Fetching from the server

ハードコーディングしてあるデータをサーバーから受け取るように書き換えよう。
データプロパティを取り除き、フェッチするURLに置き換える。

```
// tutorial11.js
ReactDOM.render(
  <CommentBox url="/api/comments" />,
  document.getElementById('content')
);
```

このコンポーネントは先ほどまでのコンポーネントとは異なる。なぜなら自身で再度レンダリングする必要があるからだ。
コンポーネントにはサーバーからのリクエストが返ってくるまで何もデータがなく、返ってきたら新しいコメントをレンダリングする必要がある。

#### Reactive state

これまではプロパティに基づいて各コンポーネントは1回だけレンダリングをしてきた。
`props`は不変のものだったのだ。親からもらったり、自身で所持していたり。
インタラクションを実装するために、コンポーネントにmutable(変わりやすい)stateを紹介する。
`this.state`はprovateであり、`this.setState()`を呼び出すことで変更される。
ステートが更新されると、コンポーネントは自身を再レンダリングする。

`render()`メソッドは`this.props`と`this.state`の関数の宣言的に記述されている。
フレームワークはUIが常に入力に対して安定していることを保証する。

サーバーがデータをフェッチすると、所持しているコメントデータを変更する必要がある。
`CommentBox`コンポーネントに自身のステートとしてコメントデータの配列を追加しよう。

```
// tutorial12.js
var CommentBox = React.createClass({
  getInitialState: function() {
    return {data: []};
  },
  render: function() {
    return (
      <div className="commentBox">
        <h1>Comments</h1>
        <CommentList data={this.state.data} />
        <CommentForm />
      </div>
    );
  }
});
```

`getInitialState()`はコンポーネントのライフサイクルの中で正確に一度だけ実行され、コンポーネントの初期ステートをセットアップする。

#### Updating state

コンポーネントが最初に作成されたら、サーバーからJSONをGETし、最新のデータを反映させるためにステートを更新したい。
サーバーへの非同期通信を行い、必要なデータを手早くフェッチするためにjQueryを使うつもりだ。
起動しているサーバーの中には既にデータが含まれている。(`comments.json`)
なので一度フェッチして、`this.state.date`を見ていこう。

```
[
  {"author": "Pete Hunt", "text": "This is one comment"},
  {"author": "Jordan Walke", "text": "This is *another* comment"}
]
```

```
// tutorial13.js
var CommentBox = React.createClass({
  getInitialState: function() {
    return {data: []};
  },
  componentDidMount: function() {
    $.ajax({
      url: this.props.url,
      dataType: 'json',
      cache: false,
      success: function(data) {
        this.setState({data: data});
      }.bind(this),
      error: function(xhr, status, err) {
        console.error(this.props.url, status, err.toString());
      }.bind(this)
    });
  },
  render: function() {
    return (
      <div className="commentBox">
        <h1>Comments</h1>
        <CommentList data={this.state.data} />
        <CommentForm />
      </div>
    );
  }
});
```

`componentDidMount`メソッドはコンポーネントが最初にレンダリングされた際にReactから自動的に呼び出されるメソッドである。
ダイナミックな更新のキーとなっているのが`this.setState()`の呼び出しである。
古いコメントの配列をサーバーから取得した新しい物に置き換え、UIを自動的に更新する。
このリアクティブ性のために、小さな変更を加えライブアップデートを実現する。
ここではシンプルなポーリングを行うが、WebSocketやその他のテクノロジーを使うため簡単にできる。

```
// tutorial14.js
var CommentBox = React.createClass({
  loadCommentsFromServer: function() {
    $.ajax({
      url: this.props.url,
      dataType: 'json',
      cache: false,
      success: function(data) {
        this.setState({data: data});
      }.bind(this),
      error: function(xhr, status, err) {
        console.error(this.props.url, status, err.toString());
      }.bind(this)
    });
  },
  getInitialState: function() {
    return {data: []};
  },
  componentDidMount: function() {
    this.loadCommentsFromServer();
    setInterval(this.loadCommentsFromServer, this.props.pollInterval);
  },
  render: function() {
    return (
      <div className="commentBox">
        <h1>Comments</h1>
        <CommentList data={this.state.data} />
        <CommentForm />
      </div>
    );
  }
});

ReactDOM.render(
  <CommentBox url="/api/comments" pollInterval={2000} />,
  document.getElementById('content')
);
```

この変更で行ったことはAJAX呼び出しのメソッドを分割して移動し、そのメソッドをコンポーネントの初期読み込み時とその後毎2秒おきに呼び出すというものだ。試しにブラウザで実行し`comments.json`を変更してみよう。
2秒以内に変更が反映されるはずだ！

<hr>

### Adding new comments

ここではフォームを組み立てよう。`CommentForm`コンポーネントはユーザーに名前とコメントテキストを求め、コメントを保存するためにサーバーへ送信する。

```
// tutorial15.js
var CommentForm = React.createClass({
  render: function() {
    return (
      <form className="commentForm">
        <input type="text" placeholder="Your name" />
        <input type="text" placeholder="Say something..." />
        <input type="submit" value="Post" />
      </form>
    );
  }
});
```

#### Controlled components

伝統的なDOMでは、`input`要素はレンダリングされ、ブラウザはステートを管理する。
その結果、実際のDOMのステートとコンポーネントのステートの間に差分が生じる。
ビューのステートがコンポーネントのステートを異なるのは理想的ではない。
Reactの場合、初期化の時だけでなくコンポーネントは常にビューのステートを表す。

それゆえに、入力されたユーザーのインプットを保存するには`this.state`を用いる。
私たちは初期の`state`を２つのプロパティ、`author`と`text`で定義し、それらに空の文字列を入れた。
Reactの`<input>`要素では、コンポーネントの`state`を反映させるための`value`プロパティをセットし、`onChange`ハンドラをつけた。これらの`value`つきの`<input>`要素は"controlled components"と呼ばれる。
より詳しく知りければ[こちら](https://facebook.github.io/react/docs/forms.html#controlled-components)

```
var CommentForm = React.createClass({
  getInitialState: function() {
    return {author: '', text: ''};
  },
  handleAuthorChange: function(e) {
    this.setState({author: e.target.value});
  },
  handleTextChange: function(e) {
    this.setState({text: e.target.value});
  },
  render: function() {
    return (
      <form className="commentForm">
        <input
          type="text"
          placeholder="Your name"
          value={this.state.author}
          onChange={this.handleAuthorChange}
        />
        <input
          type="text"
          placeholder="Say something..."
          value={this.state.text}
          onChange={this.handleTextChange}
        />
        <input type="submit" value="Post" />
      </form>
    );
  }
});
```

#### Events

Reactではコンポーネントにイベントハンドラを付けるときにキャメルケースを使う。
私たちは２つの`<input>`要素に`onChange`というイベントハンドラをつけた。
ここで、ユーザーが`<input>`フィールドにテキストを入力した場合、`onChange`コールバックは発火し、コンポーネントの`state`が変更される。

その後`input`要素のレンダリングされた値は現在のコンポーネントの`state`を反映するために更新される。

#### Submitting the form

インタラクティブなフォームを作成しよう！
ユーザーがフォームを送信する時、それらをクリアする必要があり、リクエストをサーバーに送信し、そしてコメントのリストをリフレッシュする。
はじめに、フォームの送信イベントをキャッチしてクリアしよう。

```
// tutorial17.js
var CommentForm = React.createClass({
  getInitialState: function() {
    return {author: '', text: ''};
  },
  handleAuthorChange: function(e) {
    this.setState({author: e.target.value});
  },
  handleTextChange: function(e) {
    this.setState({text: e.target.value});
  },
  handleSubmit: function(e) {
    e.preventDefault();
    var author = this.state.author.trim();
    var text = this.state.text.trim();
    if (!text || !author) {
      return;
    }
    // TODO: send request to the server
    this.setState({author: '', text: ''});
  },
  render: function() {
    return (
      <form className="commentForm" onSubmit={this.handleSubmit}>
        <input
          type="text"
          placeholder="Your name"
          value={this.state.author}
          onChange={this.handleAuthorChange}
        />
        <input
          type="text"
          placeholder="Say something..."
          value={this.state.text}
          onChange={this.handleTextChange}
        />
        <input type="submit" value="Post" />
      </form>
    );
  }
});
```

正しい入力でフォームが送信された時はフォームをクリアするような`onSubmit`ハンドラを追加した。

`preventDefault()`を呼び出してブラウザのデフォルトのフォーム送信アクションを妨げる。

#### Callbacks as props

ユーザーがコメントを送信したら、新しいコメントを追加した状態でリストをリフレッシュ留守必要がある。
この全てのロジックを`CommentBox`内で行うので、`CommentBox`はコメントリストに相応するステートを所有する。

子コンポーネントから親コンポーネントにデータを渡す必要がある。それを親の`render`メソッド内で子に新しいコールバック(`handleCommentSubmit`)を渡し、それを子の`onCommentSubmit`にバインドすることで実行する。
イベントがトリガーされた時は、コールバックが発動する。

```
// tutorial18.js
var CommentBox = React.createClass({
  loadCommentsFromServer: function() {
    $.ajax({
      url: this.props.url,
      dataType: 'json',
      cache: false,
      success: function(data) {
        this.setState({data: data});
      }.bind(this),
      error: function(xhr, status, err) {
        console.error(this.props.url, status, err.toString());
      }.bind(this)
    });
  },
  handleCommentSubmit: function(comment) {
    // TODO: submit to the server and refresh the list
  },
  getInitialState: function() {
    return {data: []};
  },
  componentDidMount: function() {
    this.loadCommentsFromServer();
    setInterval(this.loadCommentsFromServer, this.props.pollInterval);
  },
  render: function() {
    return (
      <div className="commentBox">
        <h1>Comments</h1>
        <CommentList data={this.state.data} />
        <CommentForm onCommentSubmit={this.handleCommentSubmit} />
      </div>
    );
  }
});
```

ユーザがフォームを送信した時は`CommentForm`からコールバックを呼びだそう。

```
// tutorial19.js
var CommentForm = React.createClass({
  getInitialState: function() {
    return {author: '', text: ''};
  },
  handleAuthorChange: function(e) {
    this.setState({author: e.target.value});
  },
  handleTextChange: function(e) {
    this.setState({text: e.target.value});
  },
  handleSubmit: function(e) {
    e.preventDefault();
    var author = this.state.author.trim();
    var text = this.state.text.trim();
    if (!text || !author) {
      return;
    }
    this.props.onCommentSubmit({author: author, text: text});
    this.setState({author: '', text: ''});
  },
  render: function() {
    return (
      <form className="commentForm" onSubmit={this.handleSubmit}>
        <input
          type="text"
          placeholder="Your name"
          value={this.state.author}
          onChange={this.handleAuthorChange}
        />
        <input
          type="text"
          placeholder="Say something..."
          value={this.state.text}
          onChange={this.handleTextChange}
        />
        <input type="submit" value="Post" />
      </form>
    );
  }
});
```

コールバックについて平たく言えば、サーバーに送信してリストをリフレッシュするんだ。

```
// tutorial20.js
var CommentBox = React.createClass({
  loadCommentsFromServer: function() {
    $.ajax({
      url: this.props.url,
      dataType: 'json',
      cache: false,
      success: function(data) {
        this.setState({data: data});
      }.bind(this),
      error: function(xhr, status, err) {
        console.error(this.props.url, status, err.toString());
      }.bind(this)
    });
  },
  handleCommentSubmit: function(comment) {
    $.ajax({
      url: this.props.url,
      dataType: 'json',
      type: 'POST',
      data: comment,
      success: function(data) {
        this.setState({data: data});
      }.bind(this),
      error: function(xhr, status, err) {
        console.error(this.props.url, status, err.toString());
      }.bind(this)
    });
  },
  getInitialState: function() {
    return {data: []};
  },
  componentDidMount: function() {
    this.loadCommentsFromServer();
    setInterval(this.loadCommentsFromServer, this.props.pollInterval);
  },
  render: function() {
    return (
      <div className="commentBox">
        <h1>Comments</h1>
        <CommentList data={this.state.data} />
        <CommentForm onCommentSubmit={this.handleCommentSubmit} />
      </div>
    );
  }
});
```

<hr>

### Optimization: optimistic updates

このアプリケーションについて機能は完璧であるが、コメントが表示されるまでリクエストが完了するのを待たなければならないため遅いように感じる。
アプリがより早く感じられるようにコメントをリストに追加することが可能だ。

```
// tutorial21.js
var CommentBox = React.createClass({
  loadCommentsFromServer: function() {
    $.ajax({
      url: this.props.url,
      dataType: 'json',
      cache: false,
      success: function(data) {
        this.setState({data: data});
      }.bind(this),
      error: function(xhr, status, err) {
        console.error(this.props.url, status, err.toString());
      }.bind(this)
    });
  },
  handleCommentSubmit: function(comment) {
    var comments = this.state.data;
    // Optimistically set an id on the new comment. It will be replaced by an
    // id generated by the server. In a production application you would likely
    // not use Date.now() for this and would have a more robust system in place.
    comment.id = Date.now();
    var newComments = comments.concat([comment]);
    this.setState({data: newComments});
    $.ajax({
      url: this.props.url,
      dataType: 'json',
      type: 'POST',
      data: comment,
      success: function(data) {
        this.setState({data: data});
      }.bind(this),
      error: function(xhr, status, err) {
        this.setState({data: comments});
        console.error(this.props.url, status, err.toString());
      }.bind(this)
    });
  },
  getInitialState: function() {
    return {data: []};
  },
  componentDidMount: function() {
    this.loadCommentsFromServer();
    setInterval(this.loadCommentsFromServer, this.props.pollInterval);
  },
  render: function() {
    return (
      <div className="commentBox">
        <h1>Comments</h1>
        <CommentList data={this.state.data} />
        <CommentForm onCommentSubmit={this.handleCommentSubmit} />
      </div>
    );
  }
});
```

<hr>

### Congrats!

いくつかのシンプルなステップとを押してコメントボックスを作成することができた。
[何故Reactを使うのか](https://facebook.github.io/react/docs/why-react.html)、もしくは[APIリファレンス](https://facebook.github.io/react/docs/top-level-api.html)について学び開発を始めよう！
