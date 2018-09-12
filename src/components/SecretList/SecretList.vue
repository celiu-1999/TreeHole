<template>
  <div class="secret-list">
    <header class="secret_header clearfloat">
      <span class="iconfont icon-close secret_l" @click="JumpIndex"></span>
      <span class="secret_c secret_t"><i ref="secret_t_x" v-show="showTitleScale" class="secret_t_s">{{treeHoleData.treeHoleName}}之海</i></span>
      <span class="secret_r" @click="showScreen=true">筛选</span>
    </header>
    <div class="secret" ref="secretList">
      <div class="secret-wrapper">
        <p class="drop-down" v-show="dropDown">
          <img src="/static/img/svg-loaders/tail-spin.svg" alt="">
        </p>
        <div class="secret_title" ref="secret_t_d">
          {{treeHoleData.treeHoleName}}之海
        </div>
        <ul class="secret_w_m">
          <li @click="toSecret(item)" class="secret_item" v-for="item of screenList" :key="item.id">
            <div class="secret_i_header">
              {{item.name}}
              <span class="iconfont icon-nv secret_i_h_i nv" v-if="item.sex == 1"></span>
              <span class="iconfont icon-nan secret_i_h_i nan" v-if="item.sex == 0"></span>
            </div>
            <div class="secret_i_center">
              <i class="word" v-show="item.word != ''">#{{item.word}}</i>{{item.content}}
            </div>
            <div class="secret_i_img">
            <span class="secret_i_img_span" v-for="(img, index) of item.imgs" :key="index">
              <img :src="img" alt="">
            </span>
            </div>
            <div class="secret_i_bottom">
              <span class="secret_i_b_l">{{tool.SetDatetime(item.datetime)}} • </span>
              <span class="secret_i_b_l">{{item.likeNum}} 喜欢 • </span>
              <span class="secret_i_b_l">{{item.commentNum}} 评论</span>
              <span class="iconfont icon-bqxin secret_i_b_r"  :class="item.userLike == 1 ? 'yes' : 'no'" @click="userLike(item.id, item.likeNum)"></span>
            </div>
          </li>
        </ul>
        <p class="paddle" v-show="paddle">
          <img src="/static/img/svg-loaders/tail-spin.svg" alt="">
        </p>
      </div>
      <div v-show="screenLoading" class="loading-container">
        <loading></loading>
      </div>
    </div>
    <div class="curtain" v-show="showScreen" @click="showScreen=false"></div>
    <div class="screen" v-show="showScreen">
      <ul class="screen_sex">
        <i>全部</i>
        <li @click="screen_sex('0', $event)">同性 <i></i></li>
        <li @click="screen_sex('1', $event)">异性 <i></i></li>
      </ul>
      <ul class="screen_age">
        <i>全部</i>
        <li @click.stop="screen_age('05', $event)">05后 <i></i></li>
        <li @click="screen_age('00', $event)">00后 <i></i></li>
        <li @click="screen_age('95', $event)">95后 <i></i></li>
        <li @click="screen_age('90', $event)">90后 <i></i></li>
        <li @click="screen_age('85', $event)">85后 <i></i></li>
        <li @click="screen_age('80', $event)">80后 <i></i></li>
        <li @click="screen_age('75', $event)">80前 <i></i></li>
      </ul>
      <div class="btn">
        <button @click="GetScreenList">确定</button>
      </div>
    </div>
    <div class="establish" @click="ToWrite">
      <i class="iconfont icon-biji"></i>
    </div>
    <div class="secretListNull" v-show="secretListNull">
      <div>
        <div class="gz">
          <i class="iconfont icon-guanzhuangyanliao"></i>
        </div>
        <div class="tx">空空如也(╯ω╰)</div>
      </div>
    </div>
    <router-view></router-view>
  </div>
</template>

<script>
import BScroll from 'better-scroll'
import Loading from '@/components/loading/loading.vue'
import {mapState, mapMutations} from 'vuex'
export default {
  name: 'SecretList',
  data () {
    return {
      screenList: [],
      treeHoleData: {},
      showTitleScale: false,
      showScreen: false,
      Treehole_sex_data: '',
      Treehole_age_data: '',
      dropDown: false,
      paddle: false,
      Refresh: true,
      last_num: 0,
      next_page: 10,
      secretListNull: false,
      screenLoading: true
    }
  },
  computed: {
    ...mapState(['disc'])
  },
  methods: {
    JumpIndex () {
      this.$router.back(-1)
    },
    ToWrite () {
      let id = this.$router.history.current.params.id
      this.$router.push('/write')
      this.set_jar({
        id: id,
        name: this.treeHoleData.treeHoleName,
        ask: this.treeHoleData.treeHoleAsk,
        text: this.treeHoleData.treeHoleText
      })
    },
    toSecret (item) {
      this.$router.push({
        path: this.$router.history.current.path + '/' + item.id
      })
      item.TreeholeId = this.treeHoleData.treeHoleId
      item.TreeholeName = this.treeHoleData.treeHoleName
      this.setSecret(item)
    },
    screen_age (age, e) {
      let el = e.target
      if (el.getElementsByTagName('i')[0].className === 'active') {
        el.getElementsByTagName('i')[0].classList.remove('active')
        this.Treehole_age_data = ''
        return
      }
      let dom = document.getElementsByClassName('screen_age')[0].getElementsByTagName('li')
      for (var i = 0; i < dom.length; i++) {
        dom[i].getElementsByTagName('i')[0].classList.remove('active')
      }
      el.getElementsByTagName('i')[0].classList.add('active')
      this.Treehole_age_data = age
    },
    screen_sex (sex, e) {
      let el = e.target
      if (el.getElementsByTagName('i')[0].className === 'active') {
        el.getElementsByTagName('i')[0].classList.remove('active')
        this.Treehole_sex_data = ''
        return
      }
      let dom = document.getElementsByClassName('screen_sex')[0].getElementsByTagName('li')
      for (var i = 0; i < dom.length; i++) {
        dom[i].getElementsByTagName('i')[0].classList.remove('active')
      }
      el.getElementsByTagName('i')[0].classList.add('active')
      this.Treehole_sex_data = sex
    },
    GetScreenList () {
      this.$axios.get('/api/secret_list.json', {
        params: {
          Treehole_id: this.disc.id || this.$route.params.id,
          next_page: this.next_page,
          last_num: this.last_num,
          Treehole_sex: this.Treehole_sex_data,
          Treehole_age: this.Treehole_age_data
        }
      }).then(this.getSecretInfoSucc)
      this.dropDown = false
      this.paddle = false
      this.showScreen = false
      this.screenLoading = true
      this.secretListNull = false
    },
    getSecretInfoSucc (data) {
      let ResData = data.data
      if (ResData.status === 'notLogin') {
        this.$router.push('/login')
        return
      }
      if (!ResData.data.tree_hole_data.data.length && !this.Refresh) {
        this.last_num = this.screenList.length
      } else {
        if (this.Refresh) {
          this.last_num = 0
        } else {
          this.last_num = this.next_page
        }
      }
      this.treeHoleData = {
        treeHoleId: ResData.data.tree_hole_data.id,
        treeHoleName: ResData.data.tree_hole_data.name,
        treeHoleAsk: ResData.data.tree_hole_data.ask,
        treeHoleText: ResData.data.tree_hole_data.text
      }
      this.secretListNull = false
      this.screenLoading = false
      if (this.Refresh) {
        this.screenList = ResData.data.tree_hole_data.data
      } else {
        this.screenList.concat(ResData.data.tree_hole_data.data)
      }
      if (!this.screenList.length) {
        this.secretListNull = true
        this.screenLoading = false
      }
    },
    userLike (id, likeNum) {
      this.$axios.get('/api/secret_list.json', {
        params: {
          secret_id: this.disc.id || this.$route.params.id,
          item_id: id
        }
      }).then((data) => {
      })
    },
    ...mapMutations({
      setSecret: 'SET_SECRET',
      set_jar: 'SET_JAR'
    })
  },
  mounted () {
    this.screenLoading = true
    this.GetScreenList()
    this.$nextTick(() => {
      this.scroll = new BScroll(this.$refs.secretList, {
        click: true,
        pullUpLoad: {
          threshold: -20 // 在上拉到超过底部 20px 时，触发 pullingUp 事件
        }
      })
      this.scroll.on('scroll', (scroll) => {
        // 如果下拉超过50px 就显示下拉刷新的文字
        if (scroll.y > 50) {
          this.dropDown = true
          clearTimeout(this.time)
          this.time = setTimeout(() => {
            this.GetScreenList()
            this.scroll.refresh()
          }, 2000)
        }
        if (this.scroll.maxScrollY > scroll.y + 10) {
          this.paddle = true
          clearTimeout(this.time)
          this.time = setTimeout(() => {
            this.GetScreenList()
            this.scroll.refresh()
          }, 2000)
        }
        let TitleScaleX = this.$refs.secret_t_x
        let TitleScaleD = this.$refs.secret_t_d
        var ScrollY = scroll.y
        // 下拉完毕
        if (ScrollY >= -170) {
          TitleScaleD.style.opacity = 1
          this.showTitleScale = false
          this.Refresh = true
          this.last_num = 0
          return
        }
        this.showTitleScale = true
        // 上划完毕
        if (ScrollY <= -265) {
          this.showTitleScale = true
          this.Refresh = false
          TitleScaleX.setAttribute('style', 'transform: scale(' + 1 + ',' + 1 + ') translate(0px, ' + 0 + 'px); !important')
          TitleScaleD.style.opacity = 0
          this.last_num = this.screenList.length
          return
        }
        // 标题投影逻辑
        TitleScaleX.setAttribute('style', 'transform: scale(1.9, 1.9)')
        let TranslateAttr = getComputedStyle(TitleScaleX, false)['transform']
        var TranslateArray = TranslateAttr.substring(0, TranslateAttr.length - 1).substr(7).split(',')
        // 滚动到 -170 - -265 中间
        if (ScrollY <= -170 && ScrollY >= -265) {
          this.showTitleScale = true
          this.last_num = 0
          var ScaleNum = TranslateArray[0] - (Math.abs(ScrollY) / 170) + 1
          var TranslateNum = (TranslateArray[0] - (Math.abs(ScrollY) / 170)) * 40
          var OpacityNum = 0.78 - (Math.abs(ScrollY) / 340)
          if (ScaleNum <= 1) {
            ScaleNum = 1
          }
          if (TranslateNum <= 0) {
            TranslateNum = 0
          }
          TitleScaleX.setAttribute('style', 'transform: scale(' + ScaleNum + ',' + ScaleNum + ') translate(0px, ' + TranslateNum + 'px); !important')
          TitleScaleD.style.opacity = OpacityNum
        }
      })

      // touchEnd（手指离开以后触发） 通过这个方法来监听下拉刷新
      this.scroll.on('touchEnd', (pos) => {
        // 下拉动作
        if (pos.y > 50) {
          this.last_num = 0
          this.Refresh = true
        }
        // 上拉加载 总高度>下拉的高度+10 触发加载更多
        if (this.scroll.maxScrollY > pos.y + 10) {
          this.paddle = true
          this.Refresh = false
          // 使用refresh 方法 来更新scroll  解决无法滚动的问题
          this.scroll.refresh()
        }
      })
    })
  },
  unmounted () {
  },
  components: {
    Loading
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
  .curtain
    position: absolute
    top: 0
    left: 0
    right: 0
    bottom: 0
  .drop-down
    position: absolute
    left: 0
    right: 0
    height: 100px
    text-align: center
    img
      height: 100%
      text-align: center
      margin: 0 auto
  .paddle
    margin-top: 50px
    width: 100%
    height: 100px
    text-align: center
    img
      height: 100%
      text-align: center
      margin: 0 auto
  .secret-list
    position: fixed
    z-index: 100
    top: 0
    left: 0
    bottom: 0
    right: 0
    display: flex
    flex-flow: column
    .secret_header
      z-index: 100
      position: relative
      top: 0
      left: 0
      right: 0
      height: 170px
      background-color: #006baa
      .secret_l
        position: absolute
        z-index: 10
        display: inline-block
        font-size: 50px
        padding: 60px 60px 60px 60px
        color: #fffef6
      .secret_c
        position: absolute
        top: 0
        left: 0
        right: 0
        bottom: 0
        font-size: 50px
        color: #fffef6
        display: inline-block
        width: 100%
        line-height: 170px
        text-align: center
        .secret_t_s
          display: inline-block
          transform: scale(1.9,1.9) translate(0px, 20px)
      .secret_r
        position: absolute
        right: 0
        top: 0
        z-index: 10
        font-size: 45px
        color: #fffef6
        font-weight: 300
        padding: 62px 50px 62px 0
    .secret
      flex: 1
      overflow: hidden
      width: 100%
      height: 100%
      background: url("../../../static/img/shudong.png") center no-repeat fixed
      background-position: 40px 200px
      background-color: #006baa
      background-size: 1000px 900px
      .secret-wrapper
        padding-bottom: 200px
    .secret_title
      width: 100%
      text-align: center
      font-size: 95px
      line-height: 95px
      font-weight: 800
      padding: 177px 0 77px 0
      color: #fff
    .secret_item
      width: 885px
      margin: 0 auto 10px auto
      padding: 50px
      border-radius: 15px
      background-color: #fff
      .secret_i_header
        font-size: 40px
        color: #00559b
        line-height: 40px
        .secret_i_h_i
          color: #ffcaf2
          font-size: 25px
          line-height: 50px
          margin-left: 15px
        .nv
          color: #ffcaf2
        .nan
          color: #84d1ff
      .secret_i_center
        margin-top: 10px
        font-size: 40px
        color: #00559b
        font-weight: 300
        line-height: 60px
        .word
          font-weight: 600
          color: #089bd9
          margin-right: 10px
    .secret_i_img
      margin: 25px 0
      .secret_i_img_span
        display: inline-block
        width: 192px
        height: 192px
        margin-right: 6px
        overflow: hidden
        img
          width: 100%
          height: 100%
    .secret_i_bottom
      .secret_i_b_l
        font-size: 35px
        color: #5896c2
        line-height: 45px
      .secret_i_b_r
        font-size: 40px
        float: right
        line-height: 45px
      .secret_i_b_r.yes
        color: #fdc9c8
      .secret_i_b_r.no
        color: #a2d6f4
    .screen
      position: absolute
      top: 0
      bottom: 0
      right: 0
      z-index: 101
      height: 100%
      width: 430px
      background: rgba(18, 102, 159, 0.95)
      padding: 0 0 0 50px
      border-left: 2px solid rgba(42, 118, 168, .5)
      ul
        padding: 40px 45px 0 0px
        border-bottom: 1px solid #5ea1d0
        margin-bottom: 50px
        &:last-child
          border: none
        i
          display: block
          font-size: 40px
          color: #fff
          font-weight: 800
          padding: 10px 0 32px 0px
        li
          position: relative
          display: block
          font-size: 40px
          color: #d0e1e4
          padding: 32px 0 32px 0
          i
            display: none
            position: absolute
            right: 0
            top: 0
            width: 25px
            height: 25px
            border-radius: 50%
            padding: 0
            margin: 40px 0 0 0
            background-color: #fff
          i.active
            display: inline-block
      .btn
        position: absolute
        bottom: 0
        left: 0
        right: 0
        height: 140px
        text-align: center
        button
          width: 378px
          height: 88px
          border: 2px solid #fff
          border-radius: 50px
          background: rgba(0, 0, 0, 0)
          font-size: 39px
          color: #fff
          font-weight: 800
  .loading-container
    width: 100%
    height: 100%
  .establish
    position: absolute
    bottom: 20px
    right: 40px
    i
      width: 142px
      height: 142px
      font-size: 180px
      color: #039ce5
  .secretListNull
    position: absolute
    top: 170px
    left: 0
    right: 0
    bottom: 0
    display: flex
    /*justify-content: center*/
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
