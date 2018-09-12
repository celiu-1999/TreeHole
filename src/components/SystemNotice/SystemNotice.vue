<template>
  <transition name="system">
    <div class="system">
      <div class="system_header">
        <span class="system_h_close" @click="JumpIndex">
          <i class="iconfont icon-back"></i>
        </span>
        系统通知
      </div>
      <div class="system_center" ref="system_wrapper">
        <div class="system_allNews">
          <li class="system_news clearfloat" v-for="(item, index) in system" :key="index">
            <div class="system_n_t">
              <i class="iconfont icon-icon_comment"></i>
              <span>{{item.operation}}</span>
            </div>
            <div class="system_n_c">
            </div>
            <div class="system_n_s" v-if="JSON.stringify(item.topicofc) != '{}'">
              <div class="system_n_s_t">
                <img src="/static/img/shudong_x.png" alt="">
                {{item.topicofc.treeHoleName}}
              </div>
              <div class="system_n_s_b">
                #&nbsp;{{item.topicofc.content}}
              </div>
            </div>
          </li>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
import BScroll from 'better-scroll'
export default {
  name: 'SystemNotice',
  data () {
    return {
      system: []
    }
  },
  methods: {
    JumpIndex () {
      this.$router.back(-1)
    },
    toSelect (treeHoleId, selectId) {
      // this.$router.push('/list/' + treeHoleId + '/' + selectId)
    },
    getSystem () {
      this.$axios.get('/chat/getSystem.json', {
        params: {}
      }).then(this.getsystemSucc)
    },
    getsystemSucc (data) {
      let Resdata = data.data
      this.system = Resdata.data.system
    }
  },
  mounted () {
    this.getSystem()
    this.$nextTick(() => {
      this.scroll = new BScroll(this.$refs.system_wrapper, {
        click: true
      })
    })
  }
}
</script>

<style lang="stylus" scoped>
  .system-enter-active, .system-leave-active
    transition: all 0.3s
  .system-enter, .system-leave-to
    transform: translate3d(100%, 0, 0)
  .clearfloat:after
    display:block
    clear:both
    content:""
    visibility:hidden
    height:0
  .clearfloat
    zoom:1
  .system
    z-index: 200
    position: fixed
    top: 0
    left: 0
    right: 0
    bottom: 0
    background: radial-gradient(circle, #1895cb, #138ec5, #097ab7)
    display: flex
    flex-flow: column
    .system_header
      position: relative
      height: 160px
      font-size: 45px
      color: #fff
      text-align: center
      font-weight: 600
      line-height: 160px
      .system_h_close
        position: absolute
        left: 0
        top: 0
        bottom: 0
        display: inline-block
        line-height: 50px
        padding: 55px
        i
          font-size: 50px
          color: #fff
    .system_center
      overflow: hidden
      height: 100%
    .system_allNews
      margin-top: 30px
      .system_news
        padding: 50px 50px 30px 50px
        .system_n_t
          display: flex
          font-size: 40px
          color: #fff
          font-weight: 600
          i
            display: inline-block
            font-size: 40px
            color: #ffd232
            margin-right: 20px
        .system_n_c
          margin-top: 15px
          font-size: 40px
          line-height: 60px
          color: #fff
          width: 920px
          margin-left: 60px
        .system_n_s
          padding: 40px 125px 40px 40px
          background-color: rgba(34, 132, 186, .5)
          border-radius: 35px
          margin-top: 30px
          .system_n_s_t
            display: block
            font-size: 40px
            color: #fff
            img
              width: 50px
              height: 50px
              margin-right: 5px
          .system_n_s_b
            margin: 25px 0 25px 65px
            font-size: 40px
            color: #fff
</style>
