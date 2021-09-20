<template>
  <div class="search-result">
    <van-list
      v-model="loading"
      :finished="finished"
      finished-text="没有更多了"
      @load="onLoad"
    >
      <van-cell v-for="(article, index) in list" :key="index" :title="article.title" />
    </van-list>
  </div>
</template>

<script>
import { getSearchResult } from '@/api/search'
export default {
  name: 'SearchResult',
  props: {
    searchText: {
      type: String,
      required: true
    }
  },
  data () {
    return {
      list: [],
      loading: false,
      finished: false,
      page: 1,
      perPage: 20
    }
  },
  methods: {
    async onLoad () {
      const { data } = await getSearchResult({
        page: this.page,
        per_page: this.perPage,
        q: this.searchText
      })
      console.log(data)
      this.list.push(...data.data.results)
      this.loading = false
      if (data.data.results.length) {
        this.page++
      } else {
        this.finished = true
      }
    }
  }
}
</script>

<style scoped lang="less">
  .search-result {
    position: fixed;
    left: 0;
    right: 0;
    top: 54px;
    bottom: 0;
    overflow: auto;
  }
</style>
