<template>
  <div id="app">
    <header>
      <h1>blog.yuheijotaki.com</h1>
      <nav>
        <ul class="category_list">
          <li><a href="#" data-category-id="2" v-on:click="filterCategory">Develop</a></li>
          <li><a href="#" data-category-id="3" v-on:click="filterCategory">Design</a></li>
          <li><a href="#" data-category-id="4" v-on:click="filterCategory">Others</a></li>
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
    }
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
          alert('取得できませんでした。')
        }
      })();
    },
  },
  methods: {
    filterCategory: function(event) {
      this.page = 1;
      this.posts.splice(0, 20); // 投稿をすべて削除
      const cateogoryId = event.currentTarget.getAttribute('data-category-id'); // カテゴリーの取得
      const updatedUrl = url + '&categories=' + cateogoryId; // JSON URLのアップデート
      // console.log(updatedUrl);
      (async () => {
        try {
          const res = await axios.get(updatedUrl);
          this.posts = this.posts.concat(res.data);
          event.target.className += ' is-current';
        } catch (error) {
          alert('取得できませんでした。')
        }
      })();
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
  }
}
</style>
