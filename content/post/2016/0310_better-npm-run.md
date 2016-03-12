+++
date = "2016-03-10T15:45:52+09:00"
draft = false
tags = ["better-npm-run"]
title = "better-npm-runを使ってpackage.jsonを整備"

+++

ReduxのStarterKitを触っていて気付いたのでメモ。

[benoror/better-npm-run](https://github.com/benoror/better-npm-run)

<hr>

`package.json`内でのハードコーディングを避ける。

下記のように書く。

```
"scripts": {
  "compile": "better-npm-run compile",
  "start": "better-npm-run start",
  "dev": "better-npm-run dev",
  "test": "better-npm-run test"
},
```

`better-npm-run`がどのように動作するかというと、

```
"betterScripts": {
  "compile": {
    "command": "babel-node bin/compile",
    "env": {
      "DEBUG": "app:*"
    }
  },
  "dev": {
    "command": "nodemon --exec babel-node bin/server",
    "env": {
      "NODE_ENV": "development",
      "DEBUG": "app:*"
    }
  },
  "deploy": {
    "command": "npm run clean && npm run compile",
    "env": {
      "NODE_ENV": "production",
      "DEBUG": "app:*"
    }
  },
  "start": {
    "command": "babel-node bin/server",
    "env": {
      "DEBUG": "app:*"
    }
  },
  "test": {
    "command": "babel-node ./node_modules/karma/bin/karma start build/karma.conf",
    "env": {
      "NODE_ENV": "test",
      "DEBUG": "app:*"
    }
  }
},
```

こんな感じになる。
冗長になりがちなpackage.jsonがきれいに。
