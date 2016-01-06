+++
date = "2016-01-06T18:31:41+09:00"
draft = false
title = "【Rails】ForbiddenAttributesError覚書"
tags = ["rails", "error"]
+++

Railsで`ActiveModel::ForbiddenAttributesError:`エラーが出た。

<hr>

## マスアサインメント脆弱性

モデルオブジェクトのassign_attributesメソッドの引数にハッシュもしくはparamsオブジェクトを渡せば、
モデルオブジェクトの属性を一括変更できる。フォームから送信することも可能。

管理者などがパラメータを変更する分にはいいが、権限によっては書き換えてはならないパラメータもある。
けれども知識のある人間がスクリプト書けば悪用することができてしまう。

このアプリケーションの脆弱性を**マスアサインメント脆弱性**と呼ぶ。

そしてこの脆弱性に対してRails4.0から導入された対抗策が、**Strong Parameters**。

## Strong Parameters

サンプルコード:

```
def staff_member_params
  params.require(:staff_member).permit(
    :email, :password, :family_name, :given_name,
    :family_name_kana, :given_name_kana,
    :start_date, :end_date, :suspended
  )
end
```

`params.require(:staff_member)`  
paramsオブジェクトのrequireメソッドで、paramsオブジェクトが`:staff_member`というキーと持っているかを調べる。

=> 持っていなければActionController::ParameterMissingエラー

