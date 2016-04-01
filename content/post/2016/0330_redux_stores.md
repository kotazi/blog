+++
date = "2016-03-30T18:55:03+09:00"
draft = true
tags = ['Redux']
title = "【訳読】2.3 Store|Redux"

+++

## Store

これまでに、"何かが起きた"という事実を表すActionとそれに従ってStateを更新するReducerを学んだ。

Storeは両方を持つオブジェクトである。

- アプリケーションのStateを持つ
- `getState()`を通してStateにアクセスできる
- `dispach(action)`を通してStateを更新できる
- `subscribe(listener)`でリスナを登録する
- `subscribe(listener)`の登録を解除する

注意すべきはReduxアプリケーションは単一のStoreを持つということだ。
ロジックを分離したい場合はたくさんのStoreを生成するのではなく、
[reducer composition](http://redux.js.org/docs/basics/Reducers.html#splitting-reducers)を使う。

ReducerがあればStoreを生成することは簡単。
`combineReducers()`を使って複数のReducerをひとつにした。それを読み込み`createStore()`に渡す。

```
import { createStore } from 'redux'
import todoApp from './reducers'
let store = createStore(todoApp)
```

第二引数で初期のStateを渡すこともできる。サーバーなどから値が来るときは便利。

```
let store = createStore(todoApp, window.STATE_FROM_SERVER)
```


## Dispatching Actions

Storeを作成したので動作を確認する。UIがなくても更新ロジックを確認することはできる。

```
// from `./action` とすることでActionと分かる
import { addTodo, completeTodo, setVisibilityFilter, VisibilityFilters } from './actions'

// 初期ステートをログ
console.log(store.getState())

// ステートの全ての変更をログ
// subscribe()関数はリスナ解除の関数を返すことをメモ
let unsubscribe = store.subscribe(() =>
  console.log(store.getState())
)

// Dispatch some actions
store.dispatch(addTodo('Learn about actions'))
store.dispatch(addTodo('Learn about reducers'))
store.dispatch(addTodo('Learn about store'))
store.dispatch(completeTodo(0))
store.dispatch(completeTodo(1))
store.dispatch(setVisibilityFilter(VisibilityFilters.SHOW_COMPLETED))

// Stop listening to state updates
unsubscribe()

```

こんな感じになる

![](http://i.imgur.com/zMMtoMz.png)
