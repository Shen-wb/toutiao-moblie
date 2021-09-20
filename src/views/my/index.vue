<template>
  <div class="my-container">
    <div class="header user-info" v-if="user">
      <div class="base-info">
        <div class="left">
          <van-image round class="avatar" :src="currentUser.photo" fit="cover"/>
          <span class="name">{{currentUser.name}}</span>
        </div>
        <div class="right">
          <van-button size="mini" round>编辑资料</van-button>
        </div>
      </div>
      <div class="data-stats">
        <div class="data-item">
          <span class="count">{{currentUser.art_count}}</span>
          <span class="text">头条</span>
        </div>
        <div class="data-item">
          <span class="count">{{currentUser.follow_count}}</span>
          <span class="text">关注</span>
        </div>
        <div class="data-item">
          <span class="count">{{currentUser.fans_count}}</span>
          <span class="text">粉丝</span>
        </div>
        <div class="data-item">
          <span class="count">{{currentUser.like_count}}</span>
          <span class="text">获赞</span>
        </div>
      </div>
    </div>
    <div class="header not-login" v-else>
      <div class="login-btn" @click="$router.push('/login')">
        <img class="mobile-img" src="@/assets/mobile.png" alt="mobile">
        <span class="text">登录 / 注册</span>
      </div>
    </div>
    <van-grid :column-num="2" clickable class="mb-5">
      <van-grid-item class="grid-item" style="color: #eb5253" icon="star-o" text="收藏" />
      <van-grid-item class="grid-item" style="color: #ff9d1d;" icon="clock-o" text="历史" />
    </van-grid>
    <div>
      <van-cell v-if="user" title="消息通知" is-link to="" />
      <van-cell class="mb-5" title="小智同学" is-link to="" />
      <van-cell v-if="user" class="logout" title="退出登录" @click="logout"/>
    </div>
  </div>
</template>

<script>
import { mapState } from 'vuex'
import { Dialog } from 'vant'
import { getCurrentUser } from '@/api/user'
export default {
  name: 'MyIndex',
  data () {
    return {
      currentUser: {}
    }
  },
  methods: {
    logout () {
      Dialog.confirm({
        message: '确认要退出登录吗？'
      }).then(() => {
        this.$store.commit('setUser', null)
      })
    },
    async loadCurrentUser () {
      const { data } = await getCurrentUser()
      this.currentUser = data.data
    }
  },
  computed: {
    ...mapState(['user'])
  },
  mounted () {
    this.loadCurrentUser()
  }
}
</script>

<style scoped lang="less">
  .my-container {
    .header {
      height: 180px;
      background: url(~@/assets/banner.png);
      background-size: cover;
    }
    .not-login {
      display: flex;
      justify-content: center;
      align-items: center;
      .login-btn {
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        .mobile-img {
        width: 66px;
        height: 66px;
        margin-bottom: 7px;
        }
        .text {
          font-size: 14px;
          color: #fff;
        }
      }

    }
    .user-info {
      background: url(~@/assets/banner.png);
      background-size: cover;
      .base-info {
        height: 115px;
        padding: 38px 16px 11px;
        display: flex;
        justify-content: space-between;
        align-items: center;
        .left {
          display: flex;
          align-items: center;
          .avatar {
            width: 66px;
            height: 66px;
            margin-right: 11px;
            border: 2px solid #fff;
          }
          .name {
            font-size: 15px;
            color: #fff;
          }
        }
      }
      .data-stats {
        display: flex;
        .data-item {
          height: 65px;
          display: flex;
          flex-direction: column;
          align-items: center;
          justify-content: center;
          flex: 1;
          color: #fff;
          .count {
            font-size: 16px;
            margin-bottom: 5px;
          }
          .text {
            font-size: 11px;
          }
        }
      }
    }
    .grid-item {
      height: 70px;
    }
    .logout {
      text-align: center;
      color: rgb(223, 47, 47);
    }
    .mb-5 {
      margin-bottom: 5px;
    }
  }
</style>
