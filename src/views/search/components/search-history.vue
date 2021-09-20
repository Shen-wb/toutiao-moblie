<template>
  <div class="search-histoty">
    <van-cell title="历史记录">
      <div v-if="isDelete">
        <span @click="deleteAll">全部删除</span>&nbsp;&nbsp;
        <span @click="isDelete = false">完成</span>
      </div>
      <van-icon v-else-if="searchHistoryies.length !== 0" name="delete" @click="isDelete = true"/>
    </van-cell>
    <van-cell v-for="(history, index) in searchHistoryies" :key="index" :title="history" @click="$emit('search',history)">
      <van-icon name="close" v-show="isDelete" @click.stop="onDelete(index)"/>
    </van-cell>
  </div>
</template>

<script>
import { setItem } from '@/utils/storage'
export default {
  name: 'SearchHistory',
  props: {
    searchHistoryies: {
      type: Array
    }
  },
  data () {
    return {
      isDelete: false
    }
  },
  watch: {
    searchHistoryies () {
      setItem('search-histories', this.searchHistoryies)
    }
  },
  methods: {
    onDelete (index) {
      if (this.isDelete) {
        this.searchHistoryies.splice(index, 1)
        if (this.searchHistoryies.length === 0) {
          this.isDelete = false
        }
      }
    },
    deleteAll () {
      if (this.isDelete) {
        this.searchHistoryies.splice(0, this.searchHistoryies.length)
      }
    }
  }
}
</script>

<style>

</style>
