<template>
  <transition name="inter">
    <div class="inter">
      <div class="inter_header">
        <span class="inter_h_close" @click="JumpIndex">
          <i class="iconfont icon-back"></i>
        </span>
        互动通知
      </div>
      <div class="inter_center" ref="inter_wrapper">
        <div class="inter_allNews">
          <li class="inter_news clearfloat" v-for="(item, index) in Read" :key="index">
            <div class="inter_n_t">
              <i class="iconfont icon-icon_comment"></i>
              <span>{{tool.SetDatetime(item.dateTime)}} {{item.operation}}</span>
            </div>
            <div class="inter_n_c" v-if="item.reply">
              {{item.reply}}
            </div>
            <div @click="toSelect(item.select.treeHoleId, item.select.id)" class="inter_n_s">
              <div class="inter_n_s_l">
                {{item.select.treeHoleName}}
              </div>
              <div class="inter_n_s_r">
                <div class="inter_n_s_r_t">
                  {{item.select.content}}
                </div>
                <div class="inter_n_s_r_b">
                  <span>{{item.select.liveNum}} 喜欢 •</span>
                  <span> {{item.select.commentNum}} 评论</span>
                </div>
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
  name: 'InteractionNotice',
  data () {
    return {
      Read: []
    }
  },
  methods: {
    JumpIndex () {
      this.$router.back(-1)
    },
    toSelect (treeHoleId, selectId) {
      this.$router.push('/list/' + treeHoleId + '/' + selectId)
    },
    getInter () {
      this.$axios.get('/chat/getInter.json', {
        params: {}
      }).then(this.getInterSucc)
    },
    getInterSucc (data) {
      let Resdata = data.data
      this.Read = Resdata.data.Read
    }
  },
  mounted () {
    this.getInter()
    this.$nextTick(() => {
      this.scroll = new BScroll(this.$refs.inter_wrapper, {
        click: true
      })
    })
  }
}
</script>

<style lang="stylus" scoped>
  .inter-enter-active, .inter-leave-active
    transition: all 0.3s
  .inter-enter, .inter-leave-to
    transform: translate3d(100%, 0, 0)
  .clearfloat:after
    display:block
    clear:both
    content:""
    visibility:hidden
    height:0
  .clearfloat
    zoom:1
  .inter
    z-index: 200
    position: fixed
    top: 0
    left: 0
    right: 0
    bottom: 0
    background: radial-gradient(circle, #1895cb, #138ec5, #097ab7)
    display: flex
    flex-flow: column
    .inter_header
      position: relative
      height: 160px
      font-size: 45px
      color: #fff
      text-align: center
      font-weight: 600
      line-height: 160px
      .inter_h_close
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
    .inter_center
      overflow: hidden
      height: 100%
    .inter_allNews
      margin-top: 30px
      .inter_news
        padding: 50px 50px 30px 50px
        .inter_n_t
          display: flex
          font-size: 40px
          color: #fff
          font-weight: 600
          i
            display: inline-block
            font-size: 40px
            color: #ffd232
            margin-right: 20px
        .inter_n_c
          margin-top: 15px
          font-size: 40px
          line-height: 60px
          color: #fff
          width: 920px
          margin-left: 60px
        .inter_n_s
          display: flex
          height: 200px
          padding: 40px 125px 40px 40px
          background-color: rgba(34, 132, 186, .5)
          border-radius: 35px
          margin-top: 30px
          .inter_n_s_l
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
          .inter_n_s_r
            flex: 1
            margin-left: 30px
            .inter_n_s_r_t
              font-size: 40px
              line-height: 60px
              color: #fff
              width: 100%
              overflow:hidden
              text-overflow:ellipsis
              display: -webkit-box
              -webkit-box-orient: vertical
              -webkit-line-clamp: 2
            .inter_n_s_r_b
              margin-top: 40px
              font-size: 30px
              color: #a6ddf5
</style>
