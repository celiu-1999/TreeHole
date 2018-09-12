<template>
  <div class="user_can" ref="can_wrapper">
    <div class="all_can" :class="all_can.length == 0 ? 'active' : ''">
      <div class="can" v-for="(can, index) in all_can" v-bind:key="index">
        <div class="user_can_t clearfloat">
          {{can.Month}} 月
          <i>{{can.Secret.length}} 罐</i>
        </div>
        <ul class="user_can_ul">
          <li class="user_can_li clearfloat" @click="toSelect(item)" v-for="(item, index) in can.Secret" :key="index">
            <div class="user_can_li_t">
              <i></i>
              {{item.name}}
            </div>
            <div class="user_can_li_c">
              <i class="word" v-show="item.word != ''">#{{item.word}}</i>{{item.content}}
            </div>
            <div class="user_can_li_d">
              <span class="user_can_li_d_date">{{tool.getDay(item.dataTime)}} 日</span>
              <span class="user_can_li_d_live">• {{item.liveNum}} 喜欢 </span>
              <span class="user_can_li_d_comment">• {{item.commentNum}} 评论 </span>
              <span class="user_can_li_d_read">
                <i class="iconfont icon-bqxin" :class="item.userLive == 1 ? 'active' : ''"></i>
              </span>
            </div>
          </li>
        </ul>
      </div>
      <div class="secretListNull" v-show="!all_can.length">
        <div>
          <div class="gz">
            <i class="iconfont icon-guanzhuangyanliao"></i>
          </div>
          <div class="tx">空空如也(╯ω╰)</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import BScroll from 'better-scroll'
import {mapMutations} from 'vuex'
export default {
  name: 'UserCan',
  data () {
    return {
      all_can: []
    }
  },
  methods: {
    toSelect (item) {
      this.$router.push('/list/' + item.treeHoleId + '/' + item.selectId)
      this.setSecret({
        TreeholeId: item.treeHoleId,
        TreeholeName: item.name
      })
    },
    getCan () {
      this.$axios.get('/user/getCan.json', {
        params: {
        }
      }).then(this.getCanSucc)
    },
    getCanSucc (data) {
      let Resdata = data.data
      if (Resdata.status === 'notLogin') {
        this.$router.push('/login')
        return
      }
      this.all_can = Resdata.data
    },
    ...mapMutations({
      setSecret: 'SET_SECRET'
    })
  },
  mounted () {
    this.getCan()
    this.$nextTick(() => {
      this.scroll = new BScroll(this.$refs.can_wrapper, {
        click: true
      })
    })
  }
}
</script>

<style lang="stylus" scoped>
  .clearfloat:after
    display:block
    clear:both
    content:""
    visibility:hidden
    height:0
  .clearfloat
    zoom:1
  .user_can
    flex: 1
    padding: 0 50px
    height: 100%
    overflow: hidden
  .all_can.active
    width: 100%
    height: 100%
  .all_can
    padding-bottom: 50px
    .can
      margin: 35px 0 15px 0
      .user_can_t
        position: relative
        font-size: 65px
        color: #fff
        text-align: center
        margin-bottom: 85px
        i
          position: absolute
          bottom: 0
          right: 0
          font-size: 30px
          color: #fff
      .user_can_ul
        .user_can_li
          padding: 50px 60px 50px 50px
          background-color: #fff
          border-radius: 35px
          margin-bottom: 12px
          .user_can_li_t
            font-size: 40px
            color: #005aa8
            margin-left: 15px
            line-height: 50px
            font-weight: 600
            vertical-align: top
            i
              display: inline-block
              width: 50px
              height: 50px
              background: url("/static/img/shudong_x.png") no-repeat center
              vertical-align: top
          .user_can_li_c
            margin: 20px 0 30px 0
            font-size: 40px
            line-height: 60px
            color: #005aa8
            .word
              font-weight: 600
              color: #089bd9
              margin-right: 10px
          .user_can_li_d
            font-size: 30px
            color: #5f94bb
            .user_can_li_d_read
              float: right
              i
                font-size: 40px
                color: #ccebfa
              i.active
                color: #fda19f
  .secretListNull
    height: 90%
    display: flex
    justify-content: center
    align-items: center
    .gz
      display: block
      width: 1080px
      text-align: center
      i
        font-size: 300px
        color: #d0e1e4
    .tx
      font-weight: 600
      margin-top: 30px
      width: 100%
      text-align: center
      font-size: 60px
      color: #d0e1e4
</style>
