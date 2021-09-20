<template>
  <div class="channel-edit">
    <van-cell center :border='false'>
      <div slot='title' class="channel-title">我的频道</div>
      <van-button type="danger" plain round size="mini" @click="isEdit = !isEdit">{{isEdit ? '完成' : '编辑'}}</van-button>
    </van-cell>
    <van-grid :gutter="10">
      <van-grid-item class="grid-item"
        :class="{active: index === active}"
        :icon="(isEdit && index !== 0) ? 'clear' : ''"
        v-for="(channel, index) in userChannels" :key="index" :text="channel.name"
        @click="userChannelsClick(index, channel)"
      />
    </van-grid>
  <br>
    <van-cell center :border='false'>
      <div slot='title' class="channel-title mt">频道推荐</div>
    </van-cell>
    <van-grid :gutter="10">
      <van-grid-item class="grid-item" v-for="(channel, index) in recommendChannels" :key="index" :text="channel.name" @click="add(channel)"/>
    </van-grid>
  </div>
</template>

<script>
import { getAllChannels, addUserChannel, deleteUserChannel } from '@/api/channel'
import { mapState } from 'vuex'
import { setItem, getItem } from '@/utils/storage'
export default {
  name: 'ChannelEdit',
  props: {
    userChannels: {
      type: Array,
      required: true
    },
    active: {
      type: Number,
      required: true
    }
  },
  data () {
    return {
      allChannels: [],
      isEdit: false
    }
  },
  computed: {
    recommendChannels () {
      return this.allChannels.filter((channel) => {
        return !this.userChannels.find(userChannel => {
          return userChannel.id === channel.id
        })
      })
    },
    ...mapState(['user'])
  },
  methods: {
    async loadAllChannels () {
      const { data } = await getAllChannels()
      this.allChannels = data.data.channels
    },
    async add (channel) {
      this.userChannels.push(channel)
      if (this.user) {
        await addUserChannel({
          channels: [
            { id: channel.id, seq: this.userChannels.length }
          ]
        })
        console.log(this.userChannels)
      } else {
        setItem('user-channels', this.userChannels)
      }
    },
    userChannelsClick (index, channel) {
      if (this.isEdit && index !== 0) {
        this.deleteChannel(index, channel)
      } else {
        this.switchChannel(index)
      }
    },
    async deleteChannel (index, channel) {
      if (this.active >= index) {
        this.$emit('update-active', this.active - 1)
      }
      this.userChannels.splice(index, 1)
      if (this.user) {
        await deleteUserChannel(channel.id)
      } else {
        setItem('user-channels', this.userChannels)
      }
    },
    switchChannel (index) {
      this.$emit('close')
      this.$emit('update-active', index)
    }
  },
  created () {
    this.loadAllChannels()
  }
}
</script>

<style scoped lang="less">
  .channel-edit {
    position: fixed;
    top: 50px;
    bottom: 0;
    overflow: auto;
    .channel-title {
      font-size: 16px;
      color: #333333;
    }
    /deep/ .van-button {
      width: 50px;
    }
    .grid-item {
      width: 80px;
      height: 43px;
     /deep/ .van-grid-item__content {
        background-color: #f4f5f6;
        .van-grid-item__text {
          font-size: 14px;
          color: #222;
          margin-top: 0;
        }
      }
     /deep/ .van-grid-item__icon {
       position: absolute;
       right: -6px;
       top: -6px;
       font-size: 15px;
       color: rgb(189, 187, 187);
     }
    }
    .active {
     /deep/ .van-grid-item__text {
        color: red !important;
      }
    }
  }

</style>
