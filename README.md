# Vue.js / JSON から情報を引っ張ってくる その 4

## やること

[前回](https://yuheijotaki.hatenablog.com/entry/2019/02/12/100744)の続き

> - WordPress の記事一覧を WP REST API を用いてエンドポイントを作成
> - Vue.js で、カテゴリー一覧、記事タイトルの一覧を表示させる
> - Vue.js で、カテゴリーごとの投稿一覧を表示する

#### App.vue

codepen

## つまづいたところ

#### `watch` で配列のデータを監視

前回で `watch` の仕組みは `data` に変更があった際の常時監視、実行ということが分かったのですが、`data` が配列の場合はうまくいってなさそうだったのですが、ちょっとひと手間加える必要がありました。

> 連想配列の Object を丸ごと監視したい場合は、処理は `handler: function(){},` の方に記述して、`deep: true,` が必要です。
> 連想配列の `Object` を個々に監視したい場合は、`watch` のキーをクォーテーションで囲って `'individuallyObj.aaa'` のようにします。

ここでは `categoryArray` という配列を監視するために使う。

参考：[vue\.js 2\.x その 0009 watch で配列\(array\)や連想配列\(object\)を監視する \- Motomichi Works Blog](http://motomichi-works.hatenablog.com/entry/2017/04/08/164548)

#### メソッドを `mounted` 時に走らせる

```javascript
...
mounted() {
  this.methodFunctionName();
},
...
```

ここでは `loadCategory()` でカテゴリー一覧を取得して読み込み時に表示する。

参考：[javascript \- VueJS Syntax: Running method on mount \- Stack Overflow](https://stackoverflow.com/questions/46427612/vuejs-syntax-running-method-on-mount)

#### WordPress の カテゴリーの取得

`https://blog.yuheijotaki.com/wp-json/wp/v2/categories` のように `.../wp-json/wp/v2/categories` がカテゴリー一覧の JSON URL になる。

参考：[【Vue\.js】No\.007 Vue\.js から WP REST API にアクセスし、カテゴリーを取得してみた](https://wheelchair-coder.com/sample/list-js/008/)

## まとめ

[**GitHub**](https://github.com/yuheijotaki/vue-study_20190212)

- Vue.js もそうですが JavaScript の オブジェクトや配列の操作や扱いでつまづいた。。  
  オブジェクトのキー取得、`splice` などのメソッドも覚える必要あり。
- `watch` と `computed` の違いがやっぱりいまいち分からない。今回の `computed` でもできそう。
- `this` の使い方がぼんやりなのでここも覚える必要がある。

---

**`[WIP]`** でコメント入れたように課題はたくさんありますが、触わり飽きたので次回は別の WordPress REST API を叩いてやってみようと思います。
