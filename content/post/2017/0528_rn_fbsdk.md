+++
date = "2017-05-28T12:16:06+09:00"
description = ""
title = "React Native: Facebook SDKエラー対応"
draft = false

+++

## 概要

2017/05/25にFacebook SDKの4.23.0がリリースされたらしく、それの影響で`facebook/facebook-android-sdk`にエラーが出ている。

```
:react-native-fbsdk:processReleaseResources FAILED
```

## 対応

`/node_modules/react-native-fbsdk/android/build.gradle`を以下のように書き換える。

```
// Before
compile('com.facebook.android:facebook-android-sdk:4.+')
```

```
// After
compile('com.facebook.android:facebook-android-sdk:4.22.1')
```

## 参考

- [Facebook SDK for Android Changelog 4.x](https://developers.facebook.com/docs/android/change-log-4x)
- [Facebook Sdk Android Error Building](https://stackoverflow.com/questions/44190829/facebook-sdk-android-error-building)
