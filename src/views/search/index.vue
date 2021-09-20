<template>
  <div class="search-container">
    <form action="/">
      <van-search
        @focus="isResultShow = false"
        v-model="searchText"
        placeholder="请输入搜索关键词"
        show-action
        background="rgb(50,150,250)"
        @search="onSearch(searchText)"
        @cancel="$router.back()"
      />
    </form>
    <SearchResult v-if="isResultShow" :searchText="searchText"/>
    <SearchSuggestion v-else-if="searchText" :searchText="searchText" @search="onSearch"/>
    <SearchHistory v-else :searchHistoryies="searchHistoryies" @search="onSearch"/>
  </div>
</template>

<script>
import SearchSuggestion from './components/search-suggestion'
import SearchHistory from './components/search-history'
import SearchResult from './components/search-result'
import { setItem, getItem } from '@/utils/storage'
// import {getSearchHistories} from '@/api/search'
import { mapState } from 'vuex'
export default {
  name: 'Search',
  components: { SearchSuggestion, SearchHistory, SearchResult },
  data () {
    return {
      searchText: '',
      isResultShow: false,
      searchHistoryies: []
    }
  },
  computed: {
    ...mapState(['user'])
  },
  methods: {
    onSearch (searchText) {
      this.searchText = searchText
      this.isResultShow = true
      if (this.searchHistoryies.indexOf(searchText) === -1) {
        this.searchHistoryies.unshift(searchText)
        setItem('search-histories', this.searchHistoryies)
      }
    },
    async loadSearchHistories () {
      const searchHistoryies = getItem('search-histories') || []
      /* if(this.user) {
        const { data } = await getSearchHistories()
        searchHistoryies = [...new Set([...searchHistoryies,...data.data.keywords])] // 数组去重
      } */
      this.searchHistoryies = searchHistoryies
    }
  },
  created () {
    this.loadSearchHistories()
  }
}
</script>

<style scoped lang="less">

</style>
