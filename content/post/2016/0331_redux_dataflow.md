+++
date = "2016-04-03T18:55:03+09:00"
draft = true
tags = ['Redux']
title = "【訳読】2.4 Data Flow|Redux"

+++

## Data Flow

Reduxアーキテクチャは厳格な1方向データフローである。

これは全てのデータは同じライフサイクルであり、アプリのロジックを簡単で理解しやすいものにする。

Reduxアプリのデータのライフサイクルには4つのステップがある。

1. `store.dispach(action)`を呼ぶ。

Actionはプレーンオブジェクトで何が起こったかを記述する。

```
{ type: 'LIKE_ARTICLE', articleId: 42 }
{ type: 'FETCH_USER_SUCCESS', response: { id: 3, name: 'Mary' } }
{ type: 'ADD_TODO', text: 'Read the Redux docs.' }
```

MaryがarticleId:42にいいねしたと思える。

`store.dispach(action)`はアプリケーションのどこからでも呼び出すことができ、コンポーネントを含めることができ、
XHRイベントもいける。

2. ReduxのStoreが指定したReducer関数を呼び出す。

StoreはReducerに2つの引数を渡す。現在のステートと、アクションである。
例として、ルートのReducerはこのようなものを受け取る。

```
// 現在のアプリケーションのState
let previousState = {
  visibleTodoFilter: 'SHOW_ALL',
  todos: [
    {
      text: 'Read the docs.',
      complete: false
    }
  ]
}

// アクション
let action = {
  type: 'ADD_TODO',
  text: 'Understand the flow.'
}

// Reducerに引数を渡すと、次のStateを返す
let nextState = todoApp(previousState, action)
```

3. ルートのReducerは複数のReducerのアウトプットを1つのステートツリーに連結する

ルートのReducerをどのような構造にするかはあなた次第。
Reduxには`conbineReducers()`というヘルパー関数があるので、分割するときは便利。

`combineReducers()`の動きを紹介する。
ひとつはToDoのリストのReducer、もうひとつは現在選択されているフィルタセッティング用のReducer。

```
function todos(state = [], action) {
   // Somehow calculate it...
   return nextState
 }

 function visibleTodoFilter(state = 'SHOW_ALL', action) {
   // Somehow calculate it...
   return nextState
 }

 let todoApp = combineReducers({
   todos,
   visibleTodoFilter
 })
```

アクションを送信すると`combineReducers`に返された`todoApp`が両方のReducerを呼ぶ。

```
let nextTodos = todos(state.todos, action)
let nextVisibleTodoFilter = visibleTodoFilter(state.visibleTodoFilter, action)
```

両方をひとつのステートツリーにまとめて返す。

```
return {
  todos: nextTodos,
  visibleTodoFilter: nextVisibleTodoFilter
}
```

`combineReducers`はヘルパー機能だから、必ずしも使う必要はない。

4. ReduxのStoreがルートReducerによって返された完全なスタートツリーを保存する

`store.subscribe(listener)`よって登録された全てのリスナが発動する。リスナは現在のステートを取得するために`store.getState()`を呼ぶ。

これでUIに新しいスタートを反映して更新することができる。React Reduxを使っているなら、`component.setState()`が呼ばれる。
