  
<template>
  <div>
    <form
      class="card comment-form"
      v-if="user"
      @submit.prevent="publishComment"
    >
      <div class="card-block">
        <textarea
          class="form-control"
          placeholder="Write a comment..."
          rows="3"
          v-model="commentInput"
        ></textarea>
      </div>
      <div class="card-footer">
        <img :src="user && user.image" class="comment-author-img" />
        <button class="btn btn-sm btn-primary">
          Post Comment
        </button>
      </div>
    </form>

    <p v-else>
      <nuxt-link class="" to="/login">Sign in</nuxt-link
      >&nbsp;or&nbsp;<nuxt-link class="" to="/register">sign up</nuxt-link
      >&nbsp;to add comments on this article.
    </p>

    <div class="card" v-for="comment in comments" :key="comment.id">
      <div class="card-block">
        <p class="card-text">
          {{ comment.body }}
        </p>
      </div>
      <div class="card-footer">
        <nuxt-link
          class="comment-author"
          :to="{
            name: 'profile',
            params: {
              username: comment.author.username,
            },
          }"
        >
          <img
            :src="comment.author && comment.author.image"
            class="comment-author-img"
          />
        </nuxt-link>
        &nbsp;
        <nuxt-link
          class="comment-author"
          :to="{
            name: 'profile',
            params: {
              username: comment.author.username,
            },
          }"
        >
          {{ comment.author.username }}
        </nuxt-link>
        <span class="date-posted">{{
          comment.createdAt | date('MMM DD, YYYY')
        }}</span>
      </div>
    </div>
  </div>
</template>

<script>
import {getComments,postArticleComments} from '@/api/article'
import { mapState } from 'vuex'
export default {
  name: 'ArticleComments',
  data () {
    return {
      comments: [],
      commentInput: '',
    }
  },
  props: {
    article: {
      type: Object,
      required: true
    }
  },
  async mounted () {
    if (this.article.slug) {
      const { data } = await getComments(this.article.slug)
      this.comments = data.comments
    }
  },
  computed: {
    ...mapState(['user']),
  },
    methods: {
    publishComment(e) {
      e.p
      postArticleComments(this.article.slug, {
        comment: { body: this.commentInput },
      }).then((res) => {
        this.comments.push(res.data.comment)
        this.commentInput = ''
      })
    },
  },
}
</script>

<style>

</style>