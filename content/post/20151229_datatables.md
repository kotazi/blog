+++
date = "2016-01-02T17:58:27+09:00"
draft = false
title = "【訳読】Datatablesの初期化エラー"

+++

http://datatables.net/manual/tech-notes/3

Datatablesのエラー訳してみた。

<hr>

DataTablesではテーブルをカスタマイズする広範囲のオプションを初期化時に設定することができます。これは初期化の時のみなので、それ以降にこのようなオプションを設定しようとするとエラーが出てしまいます。

### 意義

単純に、DataTablesでは初期化時に設定したオプションを後から変更することができないということです。どんな操作をしたとしても、既に[API](http://datatables.net/reference/api)を通して初期化されてしまった後ではエラーが発生します。

```
DataTables warning: table id={id} - Cannot reinitialise DataTable.
```

`{id}`の部分はエラーを引き起こしたDOMのidで置き換えられます。

### 分析

このエラーはDataTablesが選択したノードにおいて既に初期化している際に、DataTablesのコンストラクタオブジェクトを呼び出すと発生しいます。下記がサンプルになります。

```
$('#example').dataTable( {
    paging: false
} );
 
 
$('#example').dataTable( {
    searching: false
} );
```

`#example`は既にDataTableとして初期化されているので、2つ目のコードブロックが実行されるときにエラーが発生します。

### 解法

このエラーを再現するにはいくつかの方法があります。なのでこのイシューを解決するためにいくつかの方法が使われます。

#### シングル初期化

複数のDataTablesの初期化を行いたいのであれば、単に同じものを設定してください。サンプルでは、ページングと検索を消しました。

```
$('#example').dataTable( {
    paging: false,
    searching: false
} );
```

より詳しく知るためには、[こちら](initialisation options manual)のマニュアルを参考にしてください。

####  オブジェクトインスタンス検索

APIを通してDataTableの活動の参照を得ようとた時にこのエラーはしばしば起こります。
例えば、もしあなたがDataTableインスタンスを作成する関数を持っていたとしましょう。これに変更を加えようとするとエラーが出てしまいます。
この要な場合、`$.fn.dataTable.isDataTable()`を利用して、DataTableが既に存在するか確認しましょう。

```
if ( $.fn.dataTable.isDataTable( '#example' ) ) {
    table = $('#example').DataTable();
}
else {
    table = $('#example').DataTable( {
        paging: false
    } );
}
```

#### retrieve

上記サンプルのコートは魅力的ではありません。`retrieve`オプションを使って、DataTablesにあなたが初期化後にオプションを変更できないことを知っていると伝えましょう。
このオプションは`$.fn.dataTable.isDataTable()`によるチェックよりショートカットになります。

```
table = $('#example').DataTable( {
    retrieve: true,
    paging: false
} );
```


#### destroy

本当にオプション設定を変更したい場合もあるでしょう。少し特殊な方法を取ることで解決できます。
古いDatatablesを破壊して新しい物を作るのです。なるべく避けたほうがいいかもしれません。

```
table = $('#example').DataTable( {
    paging: false
} );
 
table.destroy();
 
table = $('#example').DataTable( {
    searching: false
} );
```

このサンプルでは、2つめの初期化によって`paging`オプションの設定がなくなっていることに注意してください。

ショートカットとして、`destory`オプションが存在しいます。このオプションで既存のDataTableを槊杖することができます。
下記のように使用してください。

```
$('#example').DataTable( {
    paging: false
} );
 
$('#example').DataTable( {
    destroy: true,
    searching: false
} );
```

`retrieve`オプションと`destroy`オプションは相容れないので一緒に使用することはできません。

#### Reason

初期化後にDataTablesの初期設定を変更することができない理由は、その設定を元にしたコートが多すぎるためです。(例えば、スクロールのオンオフなどは複雑過ぎます。)
その結果、DataTablesのコアコードをキープしたままにするのは利用可能ではなくなっています。注意点として、`destroy`オプションを使ってもperformanceへの影響はありません。

