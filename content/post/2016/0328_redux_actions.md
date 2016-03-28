+++
date = "2016-03-28T18:34:45+09:00"
draft = false
tags = []
title = "【訳読】2.1 Actions|Redux"

+++

[2.1 Actions|Redux](http://redux.js.org/docs/basics/Actions.html)

<hr>


## Actions

Actionはアプリケーションからの情報をStoreへ渡す。
[`store.dispatch()`](http://redux.js.org/docs/api/Store.html#dispatch)を使ってActionをStoreへ送信する。

ToDoアイテムのアクションサンプル:

```
const ADD_TODO = 'ADD_TODO'
```

```
{
  type: ADD_TODO,
  text: 'Build my first Redux app'
}
```

ActionはプレーンなJavaScriptオブジェクトである。`type`プロパティを必ず持ち、Actionの種類を示す。
Typeは文字列の定数として定義されるべきであり、アプリケーションが肥大化した際はモジュールに分割することができる。

```
import { ADD_TODO, REMOVE_TODO } from '../actionTypes'
```

`type`以外のActionオブジェクトの構造はあなた次第だ。興味があれば[Flux Standard Action](https://github.com/acdlite/flux-standard-action)を見ること。

ユニークなIDを付けることはいいかも。

```
{
  type: COMPLETE_TODO,
  index: 5
}
```


## Action Creators

**Action creators** はActionを生成する関数である。
"Action"と"Action Creator"をまとめるのは簡単なのでそのほうがいいかも。

典型的なFluxの実装では、こんな感じでAction CreatorはDispachのトリガーとなる。

```
function addTodoWithDispatch(text) {
  const action = {
    type: ADD_TODO,
    text
  }
  dispatch(action)
}
```

それに対しReduxでは、Action Creatorは単純にActionを返すだけ。

```
function addTodo(text) {
  return {
    type: ADD_TODO,
    text
  }
}
```

これによりテストがすごく簡単になる。
実際dispachするには`dispach()`関数に結果を渡すだけだ。

```
dispatch(addTodo(text))
dispatch(completeTodo(index))
```

また **bound action creator** を作成して自動的にdispachすることも可能。

```
const boundAddTodo = (text) => dispatch(addTodo(text))
const boundCompleteTodo = (index) => dispatch(completeTodo(index))
```

こんな感じで呼び出すことができる。

```
boundAddTodo(text)
boundCompleteTodo(index)
```

`dispach()`関数はStoreから`store.dispatch()`を使って直接アクセスすることができるが、[react-redux](http://github.com/gaearon/react-redux)の`connect()`は便利。
[`bindActionCreators()`](http://redux.js.org/docs/api/bindActionCreators.html)を使って自動的にたくさんのアクションを`dispatch()`関数にバインドすることもできる。

## Source Code

```
/*
 * action types
 */

export const ADD_TODO = 'ADD_TODO'
export const COMPLETE_TODO = 'COMPLETE_TODO'
export const SET_VISIBILITY_FILTER = 'SET_VISIBILITY_FILTER'

/*
 * other constants
 */

export const VisibilityFilters = {
  SHOW_ALL: 'SHOW_ALL',
  SHOW_COMPLETED: 'SHOW_COMPLETED',
  SHOW_ACTIVE: 'SHOW_ACTIVE'
}

/*
 * action creators
 */

export function addTodo(text) {
  return { type: ADD_TODO, text }
}

export function completeTodo(index) {
  return { type: COMPLETE_TODO, index }
}

export function setVisibilityFilter(filter) {
  return { type: SET_VISIBILITY_FILTER, filter }
}
```
