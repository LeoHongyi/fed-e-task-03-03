  
<template>
  <div class="editor-page">
    <div class="container page">
      <div class="row">
        <div class="col-md-10 offset-md-1 col-xs-12">
          <fieldset>
            <fieldset class="form-group">
              <input
                type="text"
                class="form-control form-control-lg"
                placeholder="Article Title"
                v-model="article.title"
              />
            </fieldset>
            <fieldset class="form-group">
              <input
                type="text"
                class="form-control"
                placeholder="What's this article about?"
                v-model="article.description"
              />
            </fieldset>
            <fieldset class="form-group">
              <textarea
                class="form-control"
                rows="8"
                placeholder="Write your article (in markdown)"
                v-model="article.body"
              ></textarea>
            </fieldset>
            <fieldset class="form-group">
              <input
                type="text"
                class="form-control"
                placeholder="Enter tags"
                v-model="tagInput"
                @keypress.enter.prevent="addTag(tagInput)"
              />
              <div class="tag-list">
                <span
                  class="tag-default tag-pill"
                  v-for="(tag, index) of article.tagList"
                  :key="tag + index"
                >
                  <i class="ion-close-round" @click="removeTag(tag)"> </i>
                  {{ tag }}
                </span>
              </div>
            </fieldset>
            <button
              class="btn btn-lg pull-xs-right btn-primary"
              type="button"
              @click="publishArticle"
            >
              Publish Article
            </button>
          </fieldset>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { postArticles, getArticle, updateArticles } from '@/api/article'
export default {
  // 在路由匹配组件渲染之前先执行中间件处理
  middleware: 'authenticated',
  name: 'EditorIndex',
  data() {
    return {
      tagInput: '',
      article: {
        title: '',
        description: '',
        body: '',
        tagList: [],
      },
    }
  },
  async mounted() {
    console.log(this.$route)
    const { params } = this.$route
    const { slug } = params
    if (slug) {
      const { data } = await getArticle(params.slug)
      const { article } = data
      this.article = { ...article }
    }
  },
  methods: {
    publishArticle() {
      console.log(this.article)
      const { params } = this.$route
      const { slug } = params
      if (slug) {
        updateArticles(slug, { article: this.article }).then((res) => {
          console.log('update res: ', res)
          this.$router.push({
            name: 'article',
            params: { slug },
          })
        })
      } else {
        postArticles({ article: this.article }).then((res) => {
          console.log('publishres: ', res)
          this.$router.push({
            name: 'article',
            params: { slug: res.data.article.slug },
          })
        })
      }
    },
    removeTag(tag) {
      const index = this.article.tagList.findIndex((item) => item === tag)
      this.article.tagList.splice(index, 1)
    },
    addTag(tag) {
      this.article.tagList.push(tag)
      this.tagInput = null
    },
  },
}
</script>

<style></style>