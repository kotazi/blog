+++
date = "2016-03-29T18:55:03+09:00"
draft = true
tags = []
title = "【訳読】2.1 Reducers|Redux"

+++


[2.2 Reducers|Redux](http://redux.js.org/docs/basics/Reducers.html)

Actionは何かが起こったという事実は述べるが、アプリケーションのStateがどのように変わったかということまで細かくは言わない。
それはReducerの仕事だ。

<hr>

## Designing the State Shape


Reduxではアプリケーションの全てのステートはシングルオブジェクトとして保たれる。

ToDoアプリでは2つのことをストアしたい。

- 現在選択されているフィルター
- 実際のToDoリスト

UI State同様に、ストアしたいデータが見つかると思う。
それは良いことだけれど、UI Stateからデータは分離するようにすること。

```
// これがState(ステート）!!
{
  visibilityFilter: 'SHOW_ALL',
  todos: [
    {
      text: 'Consider using Redux',
      completed: true,
    },
    {
      text: 'Keep all state in a single tree',
      completed: false
    }
  ]
}
```

## Handling Actions


Stateオブジェクトの形が決まったので、Reducerを準備していく。
Reducerは前のStateとActionを引数に取り、次のステートを返すピュア関数だ。

```
(previousState, action) => newState
```

`Array.prototype.reduce`関数のようだからReducerと呼ばれる。  
Reducerの中で決してしてはならないことが下記だ。

- 引数を変えること
- APIを呼び出したり、ルーティングを変更すること
- `Date.now()`や`Math.random()`といったピュアでない関数を呼ぶこと

とりあえずReducerはピュアであることを覚えておこう。
**ただ計算するだけであり、同じ引数を与えれば同じ次の結果を返す。**

最初のStateを決めるところから始めよう。最初は`undefined`のStateでReducerを呼ぶ。
そうして最初のステートを取得する。

```
import { VisibilityFilters } from './actions'

// 最初のステート
const initialState = {
  visibilityFilter: VisibilityFilters.SHOW_ALL,
  todos: []
}

function todoApp(state, action) {
  if (typeof state === 'undefined') {
    return initialState
  }

  // For now, don’t handle any actions
  // and just return the state given to us.
  return state
}
```

ES6だとこんなふうにも書ける。

```
function todoApp(state = initialState, action) {
  // For now, don’t handle any actions
  // and just return the state given to us.
  return state
}
```


`SET_VISIBILITY_FILTER`を操作する。大切なのはStateの`visibilityFilter`を変更すること。

```
function todoApp(state = initialState, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return Object.assign({}, state, {
        visibilityFilter: action.filter
      })
    default:
      return state
  }
}
```

メモ。

1. `state`を変更しないこと。`Object.assign()`を使ってコピーを作成する。  
2. `default`の時は前のステートを返すこと。

## Handling More Actions

`ADD_TODO`も追加する。

```
function todoApp(state = initialState, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return Object.assign({}, state, {
        visibilityFilter: action.filter
      })
    case ADD_TODO:
      return Object.assign({}, state, {
        todos: [
          ...state.todos,
          {
            text: action.text,
            completed: false
          }
        ]
      })    
    default:
      return state
  }
}
```

先ほどと同じように、`state`に直接書き込むことはせず、新しいオブジェクトを返す。

最後に`COMPLETE_TODO`ハンドラの実装。

```
case COMPLETE_TODO:
  return Object.assign({}, state, {
    todos: state.todos.map((todo, index) => {
      if (index === action.index) {
        return Object.assign({}, todo, {
          completed: true
        })
      }
      return todo
    })
  })
```

## Splitting Reducers

かなりごちゃごちゃしてきた。

```
function todoApp(state = initialState, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return Object.assign({}, state, {
        visibilityFilter: action.filter
      })
    case ADD_TODO:
      return Object.assign({}, state, {
        todos: [
          ...state.todos,
          {
            text: action.text,
            completed: false
          }
        ]
      })
    case COMPLETE_TODO:
      return Object.assign({}, state, {
        todos: state.todos.map((todo, index) => {
          if(index === action.index) {
            return Object.assign({}, todo, {
              completed: true
            })
          }
          return todo
        })
      })
    default:
      return state
  }
}
```

もっと綺麗に書く方法はないのか？`todos`と`visibilityFilter`は独立しているように見える。
今回の場合は`todos`はとても分離しやすい。

```
function todos(state = [], action) {
  switch (action.type) {
    case ADD_TODO:
      return [
        ...state,
        {
          text: action.text,
          completed: false
        }
      ]
    case COMPLETE_TODO:
      return state.map(todo, index) => {
        if (index === action.index) {
          return Object.assign({}, todo, {
            completed: true
          })
        }
        return todo
      }
    default:
      return state
  }
}

function todoApp(state = initialState, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return Object.assign({}, state, {
        visibilityFilter: action.filter
      })
    case ADD_TODO:
    case COMPLETE_TODO:
      return Object.assign({}, state, {
        todos: todos(state.todos, action)
      })
    default:
      return state
  }
}
```

`todos`もまた`state`を取ることに注意する。しかも配列！
これはReducer Compositionと呼ばれ、Reduxアプリの基本的なパターンである。

`visibilityFilter`ももっとできるんじゃない？

```
function visibilityFilter(state = SHOW_ALL, action) {
  switch (action.type) {
  case SET_VISIBILITY_FILTER:
    return action.filter
  default:
    return state
  }
}
```

メインのReducerを各パーツとなるステートを管理するReducerを呼び出す関数に書きなおした。
初期のステートを知る必要はない。子のReducerが初期値で何を返すかを知っていればいい。

```
function todos(state = [], action) {
  switch (action.type) {
    case ADD_TODO:
      return [
        ...state,
        {
          text: action.text,
          completed: false
        }
      ]
    case COMPLETE_TODO:
      return state.map((todo, index) => {
        if (index === action.index) {
          return Object.assign({}, todo, {
            completed: true
          })
        }
        return todo
      })
    default:
      return state
  }
}

function visibilityFilter(state = SHOW_ALL, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return action.filter
    default:
      return state
  }
}

function todoApp(state = {}, action) {
  // Actionが関係している場合だけ変更して返す
  return {
    visibilityFilter: visibilityFilter(state.visibilityFilter, action),
    todos: todos(state.todos, action)
  }
}
```

**各Recuderにとっての`state`はことなり、各ステートに対応している。**

いいかんじ！アプリが大きくなった時はReducerを分割することができる。

最後に、Reducerは[`conbineReducers()`](http://redux.js.org/docs/api/combineReducers.html)と呼ばれるものを提供する。このように書き換えることができるようになる。


```
import { combineReducers } from 'redux'

const todoApp = combineReducers({
  visibilityFilter,
  todos
})

export default todoApp
```

上記は下記と全く同じ。

```
export default function todoApp(state = {}, action) {
  return {
    visibilityFilter: visibilityFilter(state.visibilityFilter, action),
    todos: todos(state.todos, action)
  }
}
```

異なるキーや関数呼び出しを与えることができる。下2つは同じことがかいてある。

```
const reducer = combineReducers({
  a: doSomethingWithA,
  b: processB,
  c: c
})
```

```
function reducer(state = {}, action) {
  return {
    a: doSomethingWithA(state.a, action),
    b: processB(state.b, action),
    c: c(state.c, action)
  }
}
```


## Source Code

最終的なやつ。

```
// combineReducersを読み込む
import { combineReducers } from 'redux'

// 各Actionを読み込む
import { ADD_TODO, COMPLETE_TODO, SET_VISIBILITY_FILTER, VisibilityFilters } from './actions'
const { SHOW_ALL } = VisibilityFilters

function visibilityFilter(state = SHOW_ALL, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return action.filter
    default:
      return state
  }
}

function todos(state = [], action) {
  switch (action.type) {
    case ADD_TODO:
      return [
        ...state,
        {
          text: action.text,
          completed: false
        }
      ]
    case COMPLETE_TODO:
      return state.map((todo, index) => {
        if (index === action.index) {
          return Object.assign({}, todo, {
            completed: true
          })
        }
        return todo
      })
    default:
      return state
  }
}

// 親となるReducer
const todoApp = combineReducers({
  visibilityFilter,
  todos
})

export default todoApp
```
