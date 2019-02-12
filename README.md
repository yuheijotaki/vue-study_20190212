# Vue.js / JSON から情報を引っ張ってくる その 3

## やること

- WordPress の記事一覧を WP REST API を用いてエンドポイントを作成
- Vue.js で、カテゴリー一覧、記事タイトルの一覧を表示させる
- Vue.js で、カテゴリーごとの投稿一覧を表示する

## セットアップ

[前回](https://yuheijotaki.hatenablog.com/entry/2019/02/08/095922)と同じ

## Vue.js 側の処理

クリックした `<a>` 要素の `data` 属性を取得する  
[javascript \- Vue js on click get html5 attribute \- Stack Overflow](https://stackoverflow.com/questions/44324869/vue-js-on-click-get-html5-attribute)

#### App.vue

一部省略

```html
<template>
  <div id="app">
    <header>
      <h1>blog.yuheijotaki.com</h1>
      <nav>
        <ul class="category_list">
          <li>
            <a href="#" data-category-id="2" v-on:click="filterCategory"
              >Develop</a
            >
          </li>
          <li>
            <a href="#" data-category-id="3" v-on:click="filterCategory"
              >Design</a
            >
          </li>
          <li>
            <a href="#" data-category-id="4" v-on:click="filterCategory"
              >Others</a
            >
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
    </main>
  </div>
</template>

<script>
  // normalize.css を読み込む
  import "normalize.css";
  // Ajax通信ライブラリ
  import axios from "axios";
  // AjaxのURL定義
  const url = `https://blog.yuheijotaki.com/wp-json/wp/v2/posts?per_page=20&categories=2`;

  export default {
    name: "App",
    data() {
      return {
        posts: [],
        page: 0
      };
    },
    mounted() {
      this.page = 1;
    },
    watch: {
      page() {
        (async () => {
          try {
            const res = await axios.get(url);
            this.posts = this.posts.concat(res.data);
          } catch (error) {
            alert("取得できませんでした。");
          }
        })();
      }
    },
    methods: {
      filterCategory: function(event) {
        this.page = 1;
        this.posts.splice(0, 20); // 投稿をすべて削除
        const cateogoryId = event.currentTarget.getAttribute(
          "data-category-id"
        ); // カテゴリーの取得
        const updatedUrl = url + "&categories=" + cateogoryId; // JSON URLのアップデート
        // console.log(updatedUrl);
        (async () => {
          try {
            const res = await axios.get(updatedUrl);
            this.posts = this.posts.concat(res.data);
            event.target.className += " is-current";
          } catch (error) {
            alert("取得できませんでした。");
          }
        })();
      }
    }
  };
</script>
```

## まとめ

[**GitHub**](https://github.com/yuheijotaki/vue-study_20190212)

#### 感想

- カテゴリーリンクを押して該当の記事の表示するまではできましたが、やっぱり自分で書くと不明点が多いです
- `watch` と `methods` の `(async () => { ...` はひとつにまとめれるのかな
- 変数 `url` と `updatedUrl` の扱い方が謎...
- `page` と `page()` の扱いも謎...

#### 次回以降やること

- カテゴリーを動的に取得
- カテゴリーの現在地（クラスの付与・解除）
- 次の記事を読み込む機能の追加
- ナビと投稿一覧のテンプレートを分ける

## Vue CLI（webpack）で使うコマンド

毎回見直すのでメモしておきます。

```bash
# プロジェクト作成
$ vue init webpack my-app # vue init [テンプレート名] [プロジェクト名]

# 質問
? Project name my-app # プロジェクトの名前
? Project description A Vue.js project # プロジェクトの説明
? Author Yuhei Jotaki <yuheijotaki@gmail.com> # 作者の名前とメールアドレス
? Vue build standalone # テンプレートの定義に「.vue」ファイルのみを使用するなら「Runtime-only」を選択可能
? Install vue-router? Yes # Vue Router をインストールするか否か
? Use ESLint to lint your code? No # ESLint をインストールするか否か
? Set up unit tests No # 自動テストツール をインストールするか否か
? Setup e2e tests with Nightwatch? No # Nightwatch.jsのE2Eテストフレームワークをインストールするか否か
? Should we run `npm install` for you after the project has been created? (recommended) npm # npmを使って自動インストール

# axios / sass-loader / node-sass / normalize.css のインストール
npm install -D axios sass-loader node-sass normalize.css

# 実行
npm run dev

# ビルド
npm run build
```
