<template>
  <transition name="chatview">
    <div class="chatview">
      <div class="chat_v_header">
        <span class="chat_v_h_i" @click="JumpIndex">
          <i class="iconfont icon-back"></i>
        </span>
        <span class="chat_v_h_t">{{userName}}</span>
      </div>
      <div class="chat_v_wrapper" ref="chat_view_wrapper">
        <div class="chat_v_content">
          <div class="chat_v_c_time">{{tool.getTime(newData)}}</div>
          <div class="chat_v_c_statement">
            从这个罐头开启一段对话
            <i class="iconfont icon-icon_comment"></i>
          </div>
          <div class="chat_v_c_can" @click="toSecret">
            <div class="chat_v_c_c_l">
              {{secret.treeHoleName}}
            </div>
            <div class="chat_v_c_c_r">
              <div class="chat_v_c_c_r_t">
                {{secret.content}}
              </div>
              <div class="chat_v_c_c_r_b">
                <span>{{tool.getDate(secret.dateTime)}}</span>
              </div>
            </div>
          </div>
          <div class="chat_v_c_data clearfloat">
            <ul class="clearfloat">
              <div class="clearfloat" v-for="(item, index) in chat" :key="index">
                <li class="chat_v_c_d_content" :class="item.currentUser == 1 ? 'current_user' : 'chat_user'">{{item.text}}</li>
              </div>
            </ul>
          </div>
        </div>
      </div>
      <div class="chat_input">
        <div class="chat_input_no" v-show="!ShowchatInput">
          <span @click="ShowchatInput=true">回复</span>
          <i class="iconfont icon-tupian"></i>
          <i class="iconfont icon-guanzhuangyanliao"></i>
        </div>
        <div class="chat_input_yes" v-show="ShowchatInput">
          <input type="text" placeholder="回复" v-model="ChatInput" @blur="ShowchatInput=false">
          <i class="iconfont icon-icon--1" @click="WebScktSendInput"></i>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
import BScroll from 'better-scroll'
export default {
  name: 'ChatView',
  data () {
    return {
      ChatInput: '',
      userName: '',
      newData: '',
      secret: [],
      chat: [],
      ShowchatInput: false
    }
  },
  methods: {
    JumpIndex () {
      this.$router.back(-1)
    },
    toSecret () {
      this.$router.push('/list/' + this.secret.treeHoleId + '/' + this.secret.id)
    },
    Refresh () {
      this.$nextTick(() => {
        this.scroll.refresh()
        this.scroll.scrollTo(0, this.scroll.maxScrollY)
      })
    },
    GetChat () {
      this.$axios.get('/chat/getChatId.json', {
        params: {
          id: this.$router.history.current.params.id
        }
      }).then(this.GetChatSucc)
    },
    GetChatSucc (data) {
      let ResData = data.data
      this.userName = ResData.data.userName
      this.newData = ResData.data.newData
      this.secret = ResData.data.secret
      this.chat = ResData.data.chat
    },
    UserReply () {
      this.$axios.get('/chat/UserReply.json', {
        params: {
          text: this.ChatInput
        }
      }).then(this.UserReplySucc)
    },
    WebScktSendInput () {
      if (this.ws.readyState === 1) {
        let a = JSON.stringify({
          'id': this.$router.history.current.params.id,
          'content': this.ChatInput
        })
        this.ws.send(a)
      }
    },
    UserReplySucc (data) {
      let text = this.ChatInput
      this.ChatInput = ''
      this.ShowchatInput = false
      this.chat.push({
        currentUser: 1,
        text: text,
        time: this.tool.getLocalTime(this.tool.currentTime)
      })
      this.Refresh()
    },
    GetChatWebSckt () {
      if (window.WebSocket) {
        this.ws = new WebSocket('ws://127.0.0.1:8000/chatroom')
        // 连接成功时的回调方法
        this.ws.onopen = this.GetChatWebScktOpen
        // 连接关闭的回调方法
        this.ws.onclose = this.GetChatWebScktClose
        // 连接发生错误的回调方法
        this.ws.onerror = this.GetChatWebScktError
        // 接收到消息的回调方法
        this.ws.onmessage = this.GetChatWebScktMessage
      }
    },
    GetChatWebScktOpen (e) {
      if (this.ws.readyState === 1) {
        console.log('webSocket连接成功')
      }
    },
    GetChatWebScktMessage (e) {
      console.log(e.data)
      // this.chat.push(JSON.parse(e.data))
      this.Refresh()
    },
    GetChatWebScktClose (e) {
      return 0
    },
    GetChatWebScktError (e) {
      console.log(e)
      console.log('连接出错')
    }
  },
  mounted () {
    this.GetChat()
    this.GetChatWebSckt()
    this.$nextTick(() => {
      this.scroll = new BScroll(this.$refs.chat_view_wrapper, {
        click: true
      })
    })
  }
}
</script>

<style lang="stylus" scoped>
  .chatview-enter-active, .chatview-leave-active
    transition: all 0.3s
  .chatview-enter, .chatview-leave-to
    transform: translate3d(100%, 0, 0)
  .clearfloat:after
    display:block
    clear:both
    content:""
    visibility:hidden
    height:0
  .clearfloat
    zoom:1
  .chatview
    z-index: 200
    position: fixed
    top: 0
    left: 0
    right: 0
    bottom: 0
    background: radial-gradient(circle, #1895cb, #138ec5, #097ab7)
    display: flex
    flex-flow: column
    .chat_v_header
      position: relative
      height: 160px
      .chat_v_h_i
        z-index: 10
        position: absolute
        display inline-block
        padding: 55px
        line-height: 45px
        i
          font-size: 45px
          color: #97bdd5
      .chat_v_h_t
        display: block
        font-size: 45px
        color: #fff
        line-height: 160px
        text-align: center
    .chat_v_wrapper
      flex: 1
      overflow: hidden
      .chat_v_content
        padding: 0 50px
        .chat_v_c_time
          padding: 15px 0
          text-align: center
          font-size: 30px
          color: #fff
        .chat_v_c_statement
          text-align: right
          padding: 30px 0
          font-size: 40px
          color: #fff
          i
            margin-left: 10px
            font-size: 40px
            color: #fbd432
        .chat_v_c_can
          display: flex
          height: 200px
          padding: 40px 125px 40px 40px
          background-color: rgba(34, 132, 186, .5)
          border-radius: 35px
          margin: 5px 0 48px 0
          .chat_v_c_c_l
            display: inline-block
            width: 200px
            height: 200px
            background: url("/static/img/shudong.png") no-repeat center
            background-size: 100%
            font-size: 30px
            color: #fff
            text-align: center
            display: flex
            align-items: center
            justify-content: center
          .chat_v_c_c_r
            flex: 1
            margin-left: 30px
            .chat_v_c_c_r_t
              font-size: 40px
              line-height: 60px
              color: #fff
              width: 100%
              overflow:hidden
              text-overflow:ellipsis
              display: -webkit-box
              -webkit-box-orient: vertical
              -webkit-line-clamp: 2
            .chat_v_c_c_r_b
              margin-top: 40px
              font-size: 30px
              color: #a6ddf5
        .chat_v_c_data
          padding-bottom: 150px
          div
            display: block
          .chat_v_c_d_content
            list-style: none
            max-width: 625px
            padding: 35px 55px 35px 40px
            border-radius: 35px
            font-size: 40px
            color: #109ad4
            line-height: 60px
            margin-bottom: 48px
          .current_user
            float: right
            border-top-right-radius: 0px
            background-color: #a0e3fe
          .chat_user
            float: left
            border-top-left-radius: 0px
            background-color: #fff
          .chat_v_c_d_time
            padding: 12px 0 35px 0
            text-align: center
            font-size: 30px
            color: #fff
    .chat_input
      position: absolute
      bottom: 0
      left: 0
      right: 0
      height: 155px
      background-color: #005788
      div
        width: 100%
        height: 100%
        display: flex
        i
          display: inline-block
          padding: 50px 50px 50px 0px
          font-size: 55px
          color: #9bbdd1
      .chat_input_no
        span
          flex: 1
          line-height: 155px
          border: none
          padding: 0
          font-size: 40px
          color: #4e87a7
          text-indent: 50px
          background-color: rgba(0, 0, 0, 0)
      .chat_input_yes
        input
          flex: 1
          border: none
          padding: 0
          font-size: 40px
          color: #fff
          text-indent: 50px
          background-color: rgba(0, 0, 0, 0)
        input::-webkit-input-placeholder
          color: #4e87a7;
        i
          font-size: 80px
          transform: rotate(45deg)
          padding: 65px 40px 50px 0px
</style>
