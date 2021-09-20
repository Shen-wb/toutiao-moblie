<template>
  <div class="article-list">
    <van-pull-refresh v-model="isPullDownLoading" @refresh="onRefresh" :success-text="refreshSuccessText">
      <van-list
        v-model="loading"
        :finished="finished"
        finished-text="没有更多了"
        @load="onLoad"
      >
        <ArticleItem
          v-for="(article,index) in articles"
          :key="index"
          :title="article.title"
          :article="article"
        />
      </van-list>
    </van-pull-refresh>
  </div>
</template>

<script>
import { getArticles } from '@/api/article'
import ArticleItem from '@/components/article-item'
export default {
  name: 'ArticleList',
  components: { ArticleItem },
  props: {
    channel: {
      type: Object,
      required: true
    }
  },
  data () {
    return {
      articles: [],
      loading: false,
      finished: false,
      timestamp: null,
      isPullDownLoading: false,
      refreshSuccessText: ''
    }
  },
  methods: {
    async onLoad () {
      const { data } = await getArticles({
        channel_id: this.channel.id,
        timestamp: this.timestamp || Date.now(),
        with_top: 1
      })
      console.log(data.data)
      this.articles.push(...data.data.results)
      this.loading = false
      if (data.data.results.length) {
        this.timestamp = data.data.pre_timestamp
      } else {
        this.finished = true
      }
    },
    async onRefresh () {
      const { data } = await getArticles({
        channel_id: this.channel.id,
        timestamp: Date.now(),
        with_top: 1
      })
      this.articles.unshift(...data.data.results)
      this.isPullDownLoading = false
      this.refreshSuccessText = '刷新成功!'
    }
  }
}
</script>

<style scoped lang="less">
  .article-list {
    position: fixed;
    left: 0;
    right: 0;
    bottom: 50px;
    top: 90px;
    overflow: auto;
  }
</style>
