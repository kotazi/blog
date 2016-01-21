+++
date = "2016-01-19T22:53:46+09:00"
draft = true
title = "【FullCalendar】Google Calendar"
tags = ["fullcalendar"]
+++

- [Google Calendar](http://fullcalendar.io/docs/google_calendar/)

<hr>

### Google Calendar

FullCalendarではGoogle Calendarのイベントを表示することができます。
GoogleCalendarはバックエンド側で管理・保持しているイベントデータを提供することができます。
(FullCalendarにはない機能)

<hr>

### Before you code...

**GoogleCalendarのAPIキーが必要です**

1. [Google Developer Console](https://console.developers.google.com/)から新しいプロジェクトを作成してください
2. そのプロジェクトのサイドバーから、「APIs & auth > APIs」と移動します
3. "Calendar API"をオンにしてください。
4. サイドバーから「APIs & auth > Credentials」と移動します
5. "Public API access"セクション内の"Create new Key"をクリックします
6. "Browser Key"を選択します
7. カレンダーをホストするドメインが分かれば入力してください。いつでも変更できるので分からなければそのままで大丈夫です
8. 新しいAPIキーが現れます。

<hr>

**Googleを公開する**

1. GoogleCalendarのインターフェイスで、左側にある"My calendars"へ
2. カレンダーにカーソルを当て、矢印をクリックします
3. メニューが表示されるので"Share this Calendar"をクリックします
4.  "Make this calendar public"にチェックします
5. "Share only my free/busy information"にチェックが入っていないことを確認してください
6. 保存します

<hr>

**GoogleCalendarのIDを取得する**

1. GoogleCalendarのインターフェイスで、左側にある"My calendars"へ
2. カレンダーにカーソルを当て、矢印をクリックします
3. メニューが表示されるので"Calendar settings"をクリックします
4. "Calendar Address"セクションのCalendarIDを見てください。 "abcd1234@group.calendar.google.com"のようなものです。

<hr>

### Dependencies

続いて、必要なJS/CSSを追加します。標準的なファイルに加え、`gcal.js`も追加します。

```
<script type='text/javascript' src='fullcalendar/gcal.js'></script>
```

<hr>

### Writing the code

JSでカレンダーを初期化する準備が整いました。これが最小のサンプルになります。

```
<script type='text/javascript'>

$(document).ready(function() {
    $('#calendar').fullCalendar({
        googleCalendarApiKey: '<YOUR API KEY>',
        events: {
            googleCalendarId: 'abcd1234@group.calendar.google.com'
        }
    });
});

</script>
```

特定の[Event Source](http://fullcalendar.io/docs/event_data/Event_Source_Object/)オプションを追加したければ、`events`オブジェクトに含めることができます。

```
<script type='text/javascript'>

$(document).ready(function() {
    $('#calendar').fullCalendar({
        googleCalendarApiKey: '<YOUR API KEY>',
        events: {
            googleCalendarId: 'abcd1234@group.calendar.google.com',
            className: 'gcal-event' // an option!
        }
    });
});

</script>
```
<hr>

### Timezones

GoogleCalendarのプラグインでは[timezone](http://fullcalendar.io/docs/timezone/timezone/)をリスペクトしています。
`false`(デフォルト)にしておくと、GoogleCalendarのタイムゾーン設定が使用されます。特定のものを指定していれば、これらは無視されます。

<Hr>
### Multiple Google Calendars

`eventSources`オプションを利用すれば複数のGoogleCalendarを利用することができます。

```
<script type='text/javascript'>

$(document).ready(function() {
    $('#calendar').fullCalendar({
        googleCalendarApiKey: '<YOUR API KEY>',
        eventSources: [
            {
                googleCalendarId: 'abcd1234@group.calendar.google.com'
            },
            {
                googleCalendarId: 'efgh5678@group.calendar.google.com',
                className: 'nice-event'
            }
        ]
    });
});

</script>
```
<Hr>
### Advanced

カレンダーごとに異なるAPIキーを利用する場合、`googleCalendarApiKey`をセットするには個々の[EventSource]を用います。

GoogleAPIのエラーを検知する必要がある場合、jQueryのAJAZエラーハンドリングには方法がありません。FullCalendarの`googleCalendarError`を使います。
