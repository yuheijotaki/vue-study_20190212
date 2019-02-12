<template>
  <div id="app">
    <header>
      <!-- <h1>blog.yuheijotaki.com</h1> -->
      <nav>
        <ul class="category_list">
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
        :class="[{
          'is-loading': loading,
          'is-disabled': disabled
        }]"
        :disabled="disabled"
        @click="load"
      >次の記事を読み込む</button>
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
      category: '',
      page: 0,
      loading: false,
      disabled: false
    }
  },
  mounted() {
    this.category = 2;
    this.page = 1;
  },
  watch: {
    page() {
      // if (this.page === null) {
      //   this.page = 1;
      // }
      this.page = 1;
      const url = `https://blog.yuheijotaki.com/wp-json/wp/v2/posts?per_page=10&page=${this.page}&categories=${this.category}`;
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
    }
  },
  methods: {
    load() {
      // `次の記事を読み込む` ボタンが押されたとき用のメソッド
      this.category = 2;
      this.loading = true;
      this.page++;
    },
    empty() {
      // 記事がない or 通信エラーのとき用のメソッド
      this.category = null;
      this.loading = false;
      this.disabled = true;
    },
    filterCategory: function(event) {
      // AjaxのURL定義
      // カテゴリーがクリックされたとき（`event` があるとき）
      // this.loading = true;
      this.loading = true;
      this.posts.splice(0, 9999);
      const cateogoryId = event.currentTarget.getAttribute('data-category-id'); // カテゴリーの取得
      this.category = cateogoryId;

      // this.page++; // これだと少しうまくいく
      // this.page = 2; // これだといかない



      // if ( event.currentTarget.getAttribute('data-category-id') ) {
        // `次の記事を読み込む` ボタンが押されたとき（`event` がないとき）
        // this.category = 2;
        // this.page ++;
      // } else {
      // }

      // this.loading = true;
      // this.page++;
      // this.posts.splice(0, 20); // 投稿をすべて削除
      // const cateogoryId = event.currentTarget.getAttribute('data-category-id'); // カテゴリーの取得
      // const updatedUrl = `${url}&page=${this.page}&categories=${cateogoryId}`; // JSON URLのアップデート
      // console.log(updatedUrl);
      // (async () => {
      //   try {
      //     const res = await axios.get(updatedUrl);
      //     this.posts = this.posts.concat(res.data);
      //     event.target.className += ' is-current';
      //     this.loading = false;
      //   } catch (error) {
      //     alert('取得できませんでした。')
      //   }
      // })();
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
