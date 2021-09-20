<template>
  <div class="search-suggestion">
    <van-cell v-for="(suggestion, index) in suggestions" :key="index" icon="search" @click="$emit('search',suggestion)">
      <div slot="title" v-html="highlight(suggestion)"></div>
    </van-cell>
  </div>
</template>

<script>
import { getSearchSuggestions } from '@/api/search'
import { debounce } from 'lodash'
export default {
  name: 'SearchSuggestion',
  props: {
    searchText: {
      type: String,
      required: true
    }
  },
  data () {
    return {
      suggestions: []
    }
  },
  methods: {
    highlight (suggestion) {
      return suggestion.replace(
        new RegExp(this.searchText, 'gi'),
        `<span style="color: red">${this.searchText}</span>`
      )
    }
  },
  watch: {
    searchText: {
      immediate: true,
      handler: debounce(async function () {
        const { data } = await getSearchSuggestions(this.searchText)
        this.suggestions = data.data.options
      }, 200)
    }
  }
}
</script>

<style>

</style>
