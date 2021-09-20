<template>
  <div class="login-container">
    <van-nav-bar
      class="app-nav-bar"
      left-arrow
      title="登录 / 注册"
      @click-left="$router.back()"
    />
    <van-form @submit="login" @failed="failed" :show-error='false' :show-error-message='false' validate-first ref="loginForm">
      <van-field
        v-model="user.mobile"
        left-icon="phone-o"
        placeholder="请输入手机号"
        name='phoneNumber'
        :rules="formRules.mobile"
      />
      <van-field
        v-model="user.code"
        clearable
        left-icon="goods-collect-o"
        placeholder="请输入验证码"
        :rules="formRules.code"
      >
        <template #button>
          <van-count-down v-if="isCountDownShow" :time="1000 * 60" format="sss" @finish="isCountDownShow = false"></van-count-down>
          <van-button v-else round :loading="isSendSmsLoading" size="small" class="send-btn" @click.prevent="sendSms">发送验证码</van-button>
        </template>
      </van-field>
      <div class="login-btn-warp">
        <van-button type="info" class="login-btn" block>登录</van-button>
      </div>
    </van-form>
  </div>
</template>

<script>
import { login, sendSms } from '@/api/user.js'
import { Toast } from 'vant'
export default {
  name: 'Login',
  data () {
    return {
      user: {
        mobile: '',
        code: ''
      },
      formRules: {
        mobile: [
          { required: true, message: '请输入手机号' },
          { pattern: /^1[3|5|7|8|9]\d{9}$/, message: '手机号格式错误！' }
        ],
        code: [
          { required: true, message: '请输入验证码' },
          { pattern: /^\d{6}$/, message: '验证码格式错误！' }
        ]
      },
      isCountDownShow: false,
      isSendSmsLoading: false
    }
  },
  methods: {
    async login () {
      Toast.loading({
        message: '登录中...',
        forbidClick: true,
        duration: 0
      })
      try {
        const { data } = await login(this.user)
        Toast.success('登录成功!')
        this.$store.commit('setUser', data.data)
        this.$router.push('/my')
      } catch (err) {
        Toast.fail('登录失败，请确认手机号和密码！')
      }
    },
    failed (err) {
      if (err.errors[0]) {
        Toast({
          mKIessage: err.errors[0].message,
          position: 'top'
        }
        )
      }
    },
    async sendSms () {
      try {
        await this.$refs.loginForm.validate('phoneNumber')
        this.isSendSmsLoading = true
        await sendSms(this.user.mobile)
        Toast('验证码已发送！')
        this.isCountDownShow = true
      } catch (err) {
        let message = ''
        if (err && err.response && err.response.status === 429) {
          message = '发送太频繁了，请稍后重试！'
        } else if (err.name = 'phoneNumber') {
          message = err.message
        } else {
          message = '发送失败，请稍后重试！'
        }
        Toast({
          message,
          position: 'top'
        })
      }
      this.isSendSmsLoading = false
    }
  }
}
</script>

<style scoped lang="less">
  .login-container {
    .login-btn-warp {
      padding: 26px 16px 0;
      .login-btn {
        background-color: #6db4fb;
        border: none;
        font-size: 15px;
      }
    }
    .send-btn {
      width: 80px;
      height: 23px;
      background-color: #ededed;
      .van-button__text {
        font-size: 11px;
        color: #666666;
      }
    }
  }
</style>
