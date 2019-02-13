<template>
  <div id="app">
    <header>
      <h1>blog.yuheijotaki.com</h1>
      <nav>
        <ul class="category_list">
          <!-- [WIP] ここもJSONからもってくる -->
          <li><a href="#" data-category-id="2" @click="filterCategory">Develop</a></li>
          <li><a href="#" data-category-id="3" @click="filterCategory">Design</a></li>
          <li><a href="#" data-category-id="4" @click="filterCategory">Others</a></li>
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
      category: '',
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
      this.posts.splice(0, 9999);
      this.loading = true;
      this.disabled = false;
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

<style lang="scss" scoped>
html,* {
  margin: 0;
  padding: 0
}
#app {
  max-width: 800px;
  margin: 40px auto;
  header {
    h1 {
      font-size: 20px;
      line-height: 1.2;
    }
    .category_list {
      margin-top: 20px;
      list-style: none;
      display: flex;
      li {
        font-size: 14px;
        line-height: 1.2;
        margin-right: 10px;
        &:last-child {
          margin-right: 0;
        }
      }
    }
  }
  main {
    margin-top: 20px;
    .post_list {
      padding-left: 1.4em;
      li {
        margin-bottom: 5px;
        font-size: 14px;
        line-height: 1.2;
        &:last-child {
          margin-bottom: 0;
        }
        a {
        }
        pre {
          font-size: 10px;
        }
      }
    }
    button {
      padding: 6px 12px;
      margin-top: 20px;
      font-size: 13px;
      line-height: 1.2;
      &.is-loading {
        background: red;
      }
      &.is-disabled {
        color: #aaa;
        background: #ccc;
      }
    }
  }
}
</style>
