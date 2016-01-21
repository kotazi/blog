+++
date = "2016-01-21T22:53:46+09:00"
draft = false
title = "【FullCalendar】Basic Usage"
tags = ["fullcalendar"]
+++

[FullCalendar](http://fullcalendar.io/)の基本的な使い方です。

- [Basic Usage](http://fullcalendar.io/docs/usage/)

<hr>

### Basic Usage

Webページにカレンダーを組み込む最初のステップは、正しいJSとCSSファイルを用意することです。
FullCalendarのスタイルシート、FullCalendar、jQuery、Momentファイルを`<head>`に含めたことを確認してください。

```
<link rel='stylesheet' href='fullcalendar/fullcalendar.css' />
<script src='lib/jquery.min.js'></script>
<script src='lib/moment.min.js'></script>
<script src='fullcalendar/fullcalendar.js'></script>
```

jQueryとMomentは必ずFullCalendarのJSファイルより先に読み込んでください。

もしドラッグやリサイズをするのであれば、追加する必要のあるライブラリがあるかもしれないので、[こちら](http://fullcalendar.io/docs/event_ui/Requirements/)を見てください。

依存関係を書き終えたら、カレンダーを初期化するためのスクリプトを記述します。
このコードはページの初期化後に実行される必要があります。最も良い方法はjQueryの`$(document).ready`あたりを使うことです。

```
$(document).ready(function() {

    // page is now ready, initialize the calendar...

    $('#calendar').fullCalendar({
        // put your options and callbacks here
    })

});
```

上記のコードは`<script>`タグで囲んでください。
ページ内のID`calendar`要素の中にカレンダーを生成します。

```
<div id='calendar'></div>
```

これで空っぽの月カレンダーが表示されると思います。
イベントの表示方法について学ぶには[Google Calendar](http://fullcalendar.io/docs/google_calendar/)もしくは[Event Data](http://fullcalendar.io/docs/event_data/)のセクションを参考にしてください。

<hr>

### Options

FullCalendarのドキュメントの大半は、オプションの影響や振る舞いについてです。
オプションはこのように、たいていカレンダーの初期化時にセットされます。

```
$('#calendar').fullCalendar({
    weekends: false // will hide Saturdays and Sundays
});
```

<hr>

### Callbacks

コールバックはオプションのように並べられますが、これらの関数は特別なことが起こらなければ呼ばれません。
下記のサンプルでは、ユーザーが日にちをクリックするとアラートが表示されるものです。

```
$('#calendar').fullCalendar({
    dayClick: function() {
        alert('a day has been clicked!');
    }
});
```
<hr>

### Methods

メソッドは様々な方法を提供します。
メソッドは既に初期化されたカレンダーのjQueryオブジェクトに対し、`fullCalendar`コマンドを買います。

```
$('#calendar').fullCalendar('next');
```

これは`next`メソッドです。カレンダーを次に進めるためのメソッドです。
