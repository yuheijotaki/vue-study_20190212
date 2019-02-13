# Vue.js / JSON から情報を引っ張ってくる その 4

## やること

[前回](https://yuheijotaki.hatenablog.com/entry/2019/02/12/100744)の続き

> - WordPress の記事一覧を WP REST API を用いてエンドポイントを作成
> - Vue.js で、カテゴリー一覧、記事タイトルの一覧を表示させる
> - Vue.js で、カテゴリーごとの投稿一覧を表示する

## Vue.js 側の処理

一番つまづいたところ、`watch` で配列のデータを監視する。

[vue\.js 2\.x その 0009 watch で配列\(array\)や連想配列\(object\)を監視する \- Motomichi Works Blog](http://motomichi-works.hatenablog.com/entry/2017/04/08/164548)

> 連想配列の Object を丸ごと監視したい場合は、処理は handler: function(){},の方に記述して、deep: true,が必要です。
> 連想配列の Object を個々に監視したい場合は、watch のキーをクォーテーションで囲って'individuallyObj.aaa'のようにします。

ここでは `categoryArray` という配列を監視するために使う。

#### App.vue

一部省略

```html

```

## まとめ

- Vue.js というより JavaScript の 配列やオブジェクトでつまづく。。
