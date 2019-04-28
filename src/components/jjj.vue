<template>
  <div class="PostList">
    <div class="loading" v-if="isLoading">
      <img src="../assets/loading.gif">
    </div>
    <div class="posts" v-else>
      <ul>
        <li>
          <div class="toobar">
            <span>全部</span>
            <span>精华</span>
            <span>分享</span>
            <span>问答</span>
            <span>招聘</span>
          </div>
        </li>
        <li v-for="post in posts">
          <img :src="post.author.avatar_url" alt="">
          <span class="allcount">
              <span class="reply_count">{{post.reply_count}}</span>
              /{{post.visit_count}}
            </span>
          <span :class="[{put_good:(post.good === true),put_top:(post.top === true),
            'topiclist-tab':(post.good !== true && post.top !== true)}]">{{post | tabFormatter}}
            </span>

          <router-link :to="{name:'post_content',
              params:{
                id: post.id,
                name:post.author.loginname
              }
            }">
            <span>{{post.title}}</span>
          </router-link>
          <span class="last_reply">{{post.last_reply_at | formatDate}}</span>
        </li>
        <li>
          <pagination @handleList="renderList"></pagination>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
  import pagination from './Pagination'

  export default {
    name: "PostList",
    data() {
      return {
        isLoading: false,
        posts: [],
        postpage: 1
      }
    },
    components: {
      pagination
    },
    methods: {
      getData() {
        this.$http.get('https://cnodejs.org/api/v1/topics', {
          params:{
            page: this.postpage,
            limit: 20
          }
        })
          .then(res => {
            this.isLoading = false
            this.posts = res.data.data
          })
          .catch(err => {
            console.log(err)
          })
      },
      renderList(value) {
        this.postpage = value
        this.getData()
      }
    },

    beforeMount() {
      this.isLoading = true
      this.getData()
    }
  }
</script>

<style scoped>

</style>
