<template>
  <div class="user_chat">
    <div class="chat_wrapper" ref="chat_wrapper">
      <div class="chat_content">
        <p class="drop-down" v-show="dropDown">
          <img src="/static/img/svg-loaders/tail-spin.svg" alt="">
        </p>
        <router-link tag="li" :to="'/user/news/' + item.chatId" class="chat" v-for="(item, index) in chat" :key="index">
          <div class="chat_l">
            {{item.treeHoleName}}
          </div>
          <div class="chat_r">
            <div class="chat_r_t">
              {{item.name}}
              <i class="iconfont icon-nv" :class="item.userSex == 1 ? 'nan': 'nv'"></i>
            </div>
            <div class="chat_r_c">
              {{item.content}}
            </div>
            <div class="chat_r_d">
              {{tool.SetDatetime(item.Datetime)}}
            </div>
          </div>
        </router-link>
        <router-link tag="li" to="/user/news/system" class="chat">
          <div class="chat_l">
            <i class="iconfont icon-laba"></i>
          </div>
          <div class="chat_r">
            <div class="chat_r_t">
              {{system.name}}
            </div>
            <div class="chat_r_c">
              {{system.content}}
            </div>
            <div class="chat_r_d">
              {{tool.SetDatetime(system.Datetime)}}
            </div>
          </div>
        </router-link>
        <router-link class="chat" v-show="!Interaction" tag="li" to="/user/news/inter">
          <div class="chat_l">
            <i class="iconfont icon-bqxin"></i>
          </div>
          <div class="chat_r">
            <div class="chat_r_t">
              {{Interaction.name}}
            </div>
            <div class="chat_r_c">
              {{Interaction.content}}
            </div>
            <div class="chat_r_d">
              {{tool.SetDatetime(Interaction.Datetime)}}
            </div>
          </div>
        </router-link>
        <p class="drop-down drop-up" v-show="dropUp">
          <img src="/static/img/svg-loaders/tail-spin.svg" alt="">
        </p>
      </div>
    </div>
    <router-view></router-view>
  </div>
</template>

<script>
import BScroll from 'better-scroll'
export default {
  name: 'NoticeChat',
  data () {
    return {
      dropDown: false,
      dropUp: false,
      chat: [],
      system: {},
      Interaction: {}
    }
  },
  methods: {
    getChat () {
      this.$axios.get('/chat/getNews.json', {
        params: {
        }
      }).then(this.getChatSucc)
    },
    getChatSucc (data) {
      let ResData = data.data
      if (ResData.status === 'notLogin') {
        this.$router.push('/login')
        return
      }
      this.dropDown = false
      this.dropUp = false
      this.chat = ResData.data.chat
      this.system = ResData.data.system
      this.Interaction = ResData.data.Interaction
      this.scroll.refresh()
      return 0
    }
  },
  mounted () {
    this.getChat()
    this.$nextTick(() => {
      this.scroll = new BScroll(this.$refs.chat_wrapper, {
        click: true,
        pullUpLoad: {
          threshold: -10 // 在上拉到超过底部 20px 时，触发 pullingUp 事件
        }
      })

      this.scroll.on('scroll', (scroll) => {
        // 如果下拉超过50px 就显示下拉刷新的文字
        if (scroll.y > 50) {
          this.dropDown = true
          clearTimeout(this.time)
          this.time = setTimeout(() => {
            this.getChat()
          }, 2000)
        }
        if (this.scroll.maxScrollY > scroll.y + 10) {
          this.dropUp = true
          clearTimeout(this.time2)
          this.time2 = setTimeout(() => {
            this.getChat()
          }, 2000)
          this.scroll.refresh()
        }
      })
      // touchEnd（手指离开以后触发） 通过这个方法来监听下拉刷新
      this.scroll.on('touchEnd', (pos) => {
        // 下拉动作
        if (pos.y > 50) {
          this.dropDown = true
        }
        // // 上拉加载 总高度>下拉的高度+10 触发加载更多
        // if (this.scroll.maxScrollY > pos.y + 10) {
        //   // 使用refresh 方法 来更新scroll  解决无法滚动的问题
        //   this.dropUp = true
        //   this.scroll.refresh()
        // }
      })
    })
  },
  components: {
  }
}
</script>

<style lang="stylus" scoped>
  .drop-down
    height: 100px
    padding: 25px 0
    text-align: center
    img
      height: 100%
      text-align: center
      margin: 0 auto
  .user_chat
    flex: 1
    overflow: hidden
    .chat_wrapper
      height: 100%
  .chat
    display: flex
    padding: 30px 70px 30px 0
    min-height: 250px
    .chat_l
      width: 195px
      margin: 0 45px
      display: flex
      align-items:center
      justify-content:center
      background: url("/static/img/shudong.png") no-repeat center
      background-size: 100%
      font-size: 35px
      color: #fff
      text-align: center
      i
        font-size: 50px
        color: #ffc3bb
    .chat_r
      flex: 1
      .chat_r_t
        font-size: 40px
        color: #fff
        font-weight: 600
        margin-bottom: 18px
        i
          font-size: 25px
          margin-left: 5px
        .nan
          color: #84d1ff
        .nv
          color: #ffc3bb
      .chat_r_c
        font-size: 40px
        line-height: 60px
        color: #fff
      .chat_r_d
        margin: 20px 0 0 0
        font-size: 40px
        color: #fff
        opacity: .5
</style>
