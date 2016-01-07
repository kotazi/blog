+++
date = "2016-01-07T18:31:41+09:00"
draft = false
title = "【Rails】Routing周りのメモ書き"
tags = ["rails", "routing"]
+++

新しいRailsプロジェクトの`routes.rb`を読んでいて調べたこととか。

<hr>

##### ◯ namespaceとscope moduleとscopeのち外

- `namespace`はURI PatternもController#Actionも指定したパスに
- `scope module`はController#Actionのみ指定したパスに
- `scope`はURI Patternのみ指定したパスに

[routeのmoduleとnamespaceとscopeの違い](http://qiita.com/blueplanet/items/522cc8364f6cf189ecad)


##### ◯ constraintsについて

constraintsを使うことでさまざまな制限を兼ねることができる。
独自のクラスを使用すうることなども可能。

[rails routing constraintsについて](http://y-yagi.tumblr.com/post/92386974040/rails-routing-constraints%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6)

##### ◯ resourcesルーティング一覧

<table class="responsive">
<thead>
<tr>
<th>HTTP動詞</th>
<th>パス</th>
<th>コントローラ#アクション</th>
<th>目的</th>
</tr>
</thead>
<tbody>
<tr>
<td>GET</td>
<td>/photos</td>
<td>photos#index</td>
<td>すべての写真の一覧を表示</td>
</tr>
<tr>
<td>GET</td>
<td>/photos/new</td>
<td>photos#new</td>
<td>写真を1つ作成するためのHTMLフォームを返す</td>
</tr>
<tr>
<td>POST</td>
<td>/photos</td>
<td>photos#create</td>
<td>写真を1つ作成する</td>
</tr>
<tr>
<td>GET</td>
<td>/photos/:id</td>
<td>photos#show</td>
<td>特定の写真を表示する</td>
</tr>
<tr>
<td>GET</td>
<td>/photos/:id/edit</td>
<td>photos#edit</td>
<td>写真編集用のHTMLフォームを1つ返す</td>
</tr>
<tr>
<td>PATCH/PUT</td>
<td>/photos/:id</td>
<td>photos#update</td>
<td>特定の写真を更新する</td>
</tr>
<tr>
<td>DELETE</td>
<td>/photos/:id</td>
<td>photos#destroy</td>
<td>特定の写真を削除する</td>
</tr>
</tbody>
</table>

<hr>

ActiveAdminとEngineについてはまた今度。