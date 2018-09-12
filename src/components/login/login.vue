<template>
  <div class="login_register_view">
    <div class="login" v-show="login">
      <div class="login_v_logo">
        <i class="iconfont icon-guanzhuangyanliao"></i>
      </div>
      <div class="login_v_title">
        <i>一罐账号登录</i>
      </div>
      <div class="login_v_login">
      <span>
        <input type="text" name="user" class="user" ref="user" placeholder="请输入用户名" @blur="inspect('user')" v-model="user">
        <div class="warning" v-show="txShow.user"><i class="iconfont icon-jinggao"></i>{{tx.user}}</div>
      </span>
      <span>
        <input type="password" name="pwd" class="pwd" ref="pwd" placeholder="请输入密码" @blur="inspect('pwd')" v-model="pwd">
        <div class="warning" v-show="txShow.pwd"><i class="iconfont icon-jinggao"></i>{{tx.pwd}}</div>
      </span>
      <span>
        <button class="cli_button" @click="LoginBtn">登录</button>
      </span>
      </div>
      <div class="login_v_paging">
        <a  @click="login=!login">注册 &gt;&gt;</a>
      </div>
    </div>
    <div class="register"  v-show="!login">
      <div class="login_v_logo">
        <i class="iconfont icon-guanzhuangyanliao"></i>
      </div>
      <div class="login_v_title">
        <i>一罐账号注册</i>
      </div>
      <div class="login_v_login">
        <span>
          <input type="text" name="user" ref="reg_user" class="user" placeholder="请输入用户名" @blur="inspect('reg_user')" v-model="reg_user">
          <div class="warning" v-show="txShow.reg_user"><i class="iconfont icon-jinggao"></i>{{tx.reg_user}}</div>
        </span>
        <span>
          <input type="number" name="phone" ref="reg_phone" class="phone" placeholder="请输入电话号" v-model="reg_phone" @blur="inspect('reg_phone')">
          <div class="warning" v-show="txShow.reg_phone"><i class="iconfont icon-jinggao"></i>{{tx.reg_phone}}</div>
        </span>
        <span>
          <input type="password" name="pwd" ref="reg_pwd" class="pwd" placeholder="请输入密码" v-model="reg_pwd" @blur="inspect('reg_pwd')">
          <div class="warning" v-show="txShow.reg_pwd"><i class="iconfont icon-jinggao"></i>{{tx.reg_pwd}}</div>
        </span>
        <span>
          <button class="cli_button" @click="registerBtn">注册</button>
        </span>
      </div>
      <div class="login_v_paging">
        <a @click="login=!login">&lt;&lt; 登录</a>
      </div>
    </div>
    <div class="ts" v-show="tsShow"><span ref="ts_c" class="ts_c">{{ts}}</span></div>
  </div>
</template>

<script>
export default {
  name: 'login',
  data () {
    return {
      login: true,
      user: '',
      pwd: '',
      reg_user: '',
      reg_phone: '',
      reg_pwd: '',
      tx: {
        user: '用户名不可为空',
        pwd: '密码不可为空',
        reg_user: '用户名不可为空',
        reg_phone: '手机号不可为空',
        reg_pwd: '密码不可为空'
      },
      txShow: {
        user: false,
        pwd: false,
        reg_user: false,
        reg_phone: false,
        reg_pwd: false
      },
      ts: '',
      tsShow: false
    }
  },
  methods: {
    JumpIndex () {
      this.$router.back(-1)
    },
    inspect (name) {
      if (name === 'user') {
        if (!this.user) {
          this.tx.user = '用户名不可为空'
          this.txShow[name] = true
        } else if (this.user.length <= 5) {
          this.tx.user = '用户名6位或以上'
          this.txShow[name] = true
        } else {
          this.txShow[name] = false
        }
      } else if (name === 'pwd') {
        this.remind(name, this.pwd, '密码不可为空', '密码6位以上')
      } else if (name === 'reg_user') {
        this.remind(name, this.reg_user, '用户名不可为空', '6位以上，仅允许字母数字')
      } else if (name === 'reg_phone') {
        this.remind(name, this.reg_phone, ' 手机号不可为空', '必须为11位，仅允许数字')
      } else if (name === 'reg_pwd') {
        this.remind(name, this.reg_pwd, '密码不可为空', '6位以上，仅允许字母数字')
      }
    },
    remind (name, text, empty, format) {
      if (!text) {
        this.tx[name] = empty
        this.txShow[name] = true
        return
      }
      if (this.verification(name, text)) {
        this.txShow[name] = false
      } else {
        this.txShow[name] = true
        this.tx[name] = format
      }
    },
    verification (name, text) {
      let reg = /^[a-zA-Z0-9_-]{6,16}$/
      if (name === 'reg_user') {
        reg = /^[a-zA-Z0-9_-]{6,16}$/
      } else if (name === 'reg_phone') {
        reg = /^((1[1-9][0-9]))\d{8}$/
      } else if (name === 'reg_pwd') {
        reg = /^(?![0-9]+$)(?![a-zA-Z]+$)[0-9A-Za-z]{6,20}$/
      }
      return reg.test(text)
    },
    LoginBtn () {
      if (this.user) {
        this.txShow.user = false
      } else {
        this.txShow.user = true
        return
      }
      if (this.pwd) {
        this.txShow.pwd = false
      } else {
        this.txShow.pwd = true
        return
      }
      this.getLogin()
    },
    registerBtn () {
      if (this.verification('reg_user', this.reg_user)) {
        this.txShow.reg_user = false
      } else {
        this.txShow.reg_user = true
        return
      }
      if (this.verification('reg_phone', this.reg_phone)) {
        this.txShow.reg_phone = false
      } else {
        this.txShow.reg_phone = true
        return
      }
      if (this.verification('reg_pwd', this.reg_pwd)) {
        this.txShow.reg_pwd = false
      } else {
        this.txShow.reg_pwd = true
        return
      }
      this.getRegister()
    },
    getLogin () {
      let params = this.$qs.stringify({
        username: this.user,
        password: this.pwd
      })
      this.$axios.post('/user/getLogin.json', params).then(this.getLoginSucc)
    },
    getLoginSucc (data) {
      if (data.data.status === 'success') {
        this.ts = data.data.msg
        this.tsShow = true
        setTimeout(() => {
          // this.$refs.ts_c.classList.add('active')
          this.tsShow = false
          this.JumpIndex()
        }, 1500)
      } else {
        this.ts = data.data.msg
        this.tsShow = true
        setTimeout(() => {
          // this.$refs.ts_c.classList.add('active')
          this.tsShow = false
        }, 1500)
      }
    },
    getRegister () {
      let params = this.$qs.stringify({
        username: this.reg_user,
        phone: this.reg_phone,
        password: this.reg_pwd
      })
      this.$axios.post('/user/getRegister.json', params).then(this.getRegisterSucc)
    },
    getRegisterSucc (data) {
      if (data.data.status === 'success') {
        this.ts = data.data.msg
        this.tsShow = true
        setTimeout(() => {
          this.tsShow = false
          this.JumpIndex()
        }, 1500)
      } else {
        this.ts = data.data.msg
        this.tsShow = true
        setTimeout(() => {
          this.tsShow = false
        }, 1500)
      }
    }
  }
}
</script>

<style lang="stylus" scoped>
  .login_v_logo
    padding: 300px 0 0 0
    text-align: center
    i
      display: inline-block
      font-size: 200px
      color: #cde1ed
      text-align: center
  .login_v_title
    padding: 50px 0 50px 0
    text-align: center
    i
      font-size: 50px
      color: #fff
  .login_v_login
    padding: 0 15% 0 15%
    span
      display: block
      padding: 10px 0 10px 0
      input
        text-indent: 20px;
        font-size: 50px
        color: #888
        width: 100%
        height: 100px
        border: 1px solid #ccc
        border-radius: 25px
      input.active
        border: 1px solid #E74C3C
      input::-webkit-input-placeholder
        font-size: 50px
        color: #ccc
      .warning
        display: block
        font-size: 40px
        color: #cde1ed
        text-align: left
        padding: 15px 0 15px 0
        i
          display: inline-block
          font-size: 50px
          padding: 0 10px 0 0
      .cli_button
        margin-top: 10px
        width: 100%
        height: 100px
        font-size: 50px
        color: #1895cb
        border-radius: 25px
        background-color: #fff
  .login_v_paging
    margin-top: 20px
    padding: 0 15%
    a
      display: inline-block
      font-size: 40px
      color: #cde1ed
  .register
    .login_v_paging
      text-align: left
  .login
    .login_v_paging
      text-align: right
  .ts
    position: fixed
    top: 0
    left: 0
    right: 0
    bottom: 0
    display: flex
    justify-content: center
    align-items: center
    .ts_c
      padding: 50px 50px
      background-color: rgba(0, 0, 0, .7)
      font-size: 50px
      color: #fff
      border-radius: 25px
      transition: all 1s
    .ts_c.active
      opacity: 0
      filter:Alpha(opacity=0)
</style>
