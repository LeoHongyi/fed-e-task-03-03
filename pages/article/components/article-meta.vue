<template>
  <div class="article-meta">
    <nuxt-link
      :to="{
        name: 'profile',
        params: {
          username: article.author.username,
        },
      }"
      ><img :src="(article.author && article.author.image) || ''"
    /></nuxt-link>
    <div class="info">
      <nuxt-link
        class="author"
        :to="{
          name: 'profile',
          params: {
            username: article.author.username,
          },
        }"
        >{{ article.author.username }}</nuxt-link
      >
      <span class="date">{{ article.createdAt | date('MMM DD, YYYY') }}</span>
    </div>
    <nuxt-link
      v-if="user.username === article.author.username"
      class="btn btn-sm btn-outline-secondary"
      :to="{
        name: 'editor',
        params: {
          slug: article.slug,
        },
      }"
    >
      <i class="ion-edit"></i>
      &nbsp; Edit Article
    </nuxt-link>
    &nbsp;&nbsp;
    <button
      v-if="user.username === article.author.username"
      class="btn btn-sm btn-outline-primary"
      @click="deletePost"
    >
      <i class="ion-trash-a"></i>
      &nbsp; Delete Article
    </button>
  </div>
</template>

<script>
import { mapState } from 'vuex'
import { deleteArticle } from '@/api/article'
export default {
  name: 'ArticleMeta',
  props: {
    article: {
      type: Object,
      required: true
    }
  },
    computed: {
    ...mapState(['user']),
  },
  methods: {
    deletePost() {
      deleteArticle(this.article.slug).then((res) => {
        this.$router.push('/')
      })
    },
  },
}
</script>

<style>

</style>