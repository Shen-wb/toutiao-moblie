<template>
  <div class="home-container">
    <van-nav-bar class="app-nav-bar">
      <van-button class="search-btn" slot="title" icon="search" type="info" round size="small" to="/search">搜索</van-button>
    </van-nav-bar>
    <van-tabs class="channel-tabs" v-model="active">
      <van-tab class="channel-tab" v-for="channel in channels" :title="channel.name" :key="channel.id">
        <ArticlLlist :channel="channel"/>
      </van-tab>
      <div class="wap-nav-placeholder" slot="nav-right"></div>
      <div class="wap-nav-wrap" slot="nav-right" @click="isChannelEditShow = true">
        <van-icon name="wap-nav" />
      </div>
    </van-tabs>
    <van-popup
      v-model="isChannelEditShow"
      position="bottom" closeable
      close-icon-position="top-left"
      get-container="body"
      style="height: 100%"
    >
      <ChannelEdit
        :userChannels="channels"
        :active="active"
        @close='isChannelEditShow = false'
        @update-active="active = $event"
      />
    </van-popup>
  </div>
</template>

<script>
import { getUserChannels } from '@/api/user'
import ArticlLlist from './components/article-list'
import ChannelEdit from './components/channel-edit'
import { mapState } from 'vuex'
import { getItem } from '@/utils/storage'
export default {
  name: 'HomeIndex',
  components: { ArticlLlist, ChannelEdit },
  data () {
    return {
      active: 0,
      channels: [],
      isChannelEditShow: false
    }
  },
  computed: {
    ...mapState(['user'])
  },
  methods: {
    async loadChannels () {
      let channels = []
      if (this.user) {
        const { data } = await getUserChannels()
        channels = data.data.channels
      } else {
        const localChannels = getItem('user-channels')
        if (localChannels) {
          channels = localChannels
        } else {
          const { data } = await getUserChannels()
          channels = data.data.channels
        }
      }
      this.channels = channels
    }
    /*  updateActive(index) {
      this.active = index
    } */
  },
  created () {
    this.loadChannels()
  }
}
</script>

<style scoped lang="less">
  .home-container {
    /deep/ .van-nav-bar__title {
      max-width: none;
    }
    .search-btn {
      width: 277px;
      height: 32px;
      background-color: #5babfb;
      border: none;
      font-size: 14px;
      .van-icon {
        font-size: 16px;
      }
    }
    .channel-tabs {
     /deep/ .van-tab {
        border-right: 1px solid #edeff3;
        border-bottom: 1px solid #edeff3;
        width: 90px;
      }
     /deep/ .van-tabs__line {
        bottom: 20px;
        width: 15px;
        height: 3px;
        background: #3296fa
      }
      .wap-nav-placeholder {
        width: 25px;
        flex-shrink: 0;
      }
      .wap-nav-wrap {
        position: fixed;
        text-align: center;
        right: 0;
        width: 33px;
        height: 44px;
        line-height: 50px;
        background-color: rgba(255, 255, 255, 0.9);
        .van-icon {
          font-size: 22px;
        }
      }
    }
  }
</style>
