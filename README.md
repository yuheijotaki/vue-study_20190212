# Vue.js / JSON から情報を引っ張ってくる その 4

## やること

[前回](https://yuheijotaki.hatenablog.com/entry/2019/02/12/100744)の続き

> - WordPress の記事一覧を WP REST API を用いてエンドポイントを作成
> - Vue.js で、カテゴリー一覧、記事タイトルの一覧を表示させる
> - Vue.js で、カテゴリーごとの投稿一覧を表示する

#### App.vue

```javascript
<template>
  <div id="app">
    <header>
      <h1>blog.yuheijotaki.com</h1>
      <nav>
        <ul class="category_list">
          <li v-for="cat in categories" :key="cat.name.rendered">
            <a href="#" v-bind:data-category-id="cat.id" @click="filterCategory">{{cat.name}}</a>
          </li>
        </ul>
      </nav>
    </header>
    <main>
      <ul class="post_list">
        <li v-for="post in posts" :key="post.title.rendered">
          <a :href="post.link" v-html="post.title.rendered"></a>
        </li>
      </ul>
      <button
        class="button"
        :class="[{ 'is-loading': loading, 'is-disabled': disabled }]"
        :disabled="disabled"
        @click="load">次の記事を読み込む</button>
    </main>
  </div>
</template>

<script>
// normalize.css を読み込む
import "normalize.css";
// Ajax通信ライブラリ
import axios from "axios";

export default {
  name: "App",
  data() {
    return {
      posts: [],
      loading: false,
      disabled: false,
      categories: '', //[WIP] ここひとつにまとめれそう
      category: '', //[WIP] ここひとつにまとめれそう
      categoryArray: [ //[WIP] ここは連想配列でなくてオブジェクトでできないか。 `data` の `category` もなくてもできそう。
        {
          id: 2,
          slug: 'develop',
          page: 0
        },
        {
          id: 3,
          slug: 'design',
          page: 0
        },
        {
          id: 4,
          slug: 'others',
          page: 0
        }
      ]
    }
  },
  mounted() {
    this.getCategories();
    this.category = 2; // 初期はDevelopカテゴリー
    this.categoryArray[0].page = 1; // 初期はDevelopカテゴリーの1ページ目
  },
  watch: {
    categoryArray: {
      handler: function(val){
        if ( this.category === 2 ) {
          var categoryPage = this.categoryArray[0].page;
        } else if ( this.category === 3 ) {
          var categoryPage = this.categoryArray[1].page;
        } else if ( this.category === 4 ) {
          var categoryPage = this.categoryArray[2].page;
        }
        const url = `https://blog.yuheijotaki.com/wp-json/wp/v2/posts?categories=${this.category}&per_page=10&page=${categoryPage}`;
        // console.log(url);
        // 非同期でJSON URLから投稿を取得
        (async () => {
          try {
            const res = await axios.get(url);
            this.posts = this.posts.concat(res.data);
            this.loading = false;
          } catch (error) {
            // alert('取得できませんでした。')
            console.log(error);
            this.empty();
          }
        })();
      },
      deep: true,
    }
  },
  methods: {
    getCategories: function () {
      const categoryUrl = `https://blog.yuheijotaki.com/wp-json/wp/v2/categories`;
      // 非同期でJSON URLから投稿を取得
      (async () => {
        try {
          const res = await axios.get(categoryUrl);
          const categories = res.data;
          categories.shift(); // [WIP] 先頭の配列（`未分類`）を削除 ただし先頭が`未分類`と限らないので要修正
          // オブジェクト `categories.page` の追加
          for ( var i = 0; i < categories.length; ++i ) {
            categories[i].page = 0;
          }
          // console.log(categories);
          this.categories = categories;
        } catch (error) {
          console.log(error);
        }
      })();
    },
    load() { // `次の記事を読み込む` ボタンが押されたとき用のメソッド
      // this.category = this.category; // カテゴリーは `this.category` のままになる
      this.loading = true;
      if ( this.category === 2 ) {
        this.categoryArray[0].page++;
      } else if ( this.category === 3 ) {
        this.categoryArray[1].page++;
      } else if ( this.category === 4 ) {
        this.categoryArray[2].page++;
      }
    },
    empty() { // 記事がない or 通信エラーのとき用のメソッド
      this.loading = false;
      this.disabled = true;
    },
    filterCategory: function(event) { // カテゴリーがクリックされたとき用のメソッド
      // カテゴリーが選択された場合は一度投稿を削除してから該当の一覧を表示させる
      this.loading = true;
      this.disabled = false;
      this.posts.splice(0, 9999); //[WIP] 0記事目から9999記事目まで削除 決め打ちなので要修正
      const categoryId = event.currentTarget.getAttribute('data-category-id'); // クリックしたカテゴリーの取得
      this.category = Number(categoryId); // `string` から `number` に変換
      if ( this.category === 2 ) {
        this.categoryArray[0].page = 0; //[WIP] 一度 `0` に戻して `1` に増加させないと `watch` が効かない
        this.categoryArray[0].page = 1;
      } else if ( this.category === 3 ) {
        this.categoryArray[1].page = 0;
        this.categoryArray[1].page = 1;
      } else if ( this.category === 4 ) {
        this.categoryArray[2].page = 0;
        this.categoryArray[2].page = 1;
      }
    }
  },
};
</script>
```

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
