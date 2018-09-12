<template>
  <transition name="secret">
    <div class="secret_view">
      <header class="secret_h">
        <span class="secret_h_return" @click="JumpIndex">
          <i class="iconfont icon-back"></i>
        </span>
          <span class="secret_h_title">
          <h4 class="title" :id="secret_data.treeHoleId">{{secret_data.treeHoleName}}之海</h4>
        </span>
        <span class="secret_h_report">
          <span class="report" @click="report=true;report_curtain=true;commentShow=false">举报</span>
        </span>
      </header>
      <div class="secret_container">
        <secret-view v-if="JSON.stringify(secret_data) != '{}'" @commentReportFun="commentReportFun" @commentReply="commentReplyInput" :ref="'view' + secret_data.id" :secret_data="secret_data"></secret-view>
      </div>
      <div class="replyInput" v-show="showReplyInput">
        <input type="text" placeholder="回复" v-model="replyInput" @blur="showReplyInput=false">
        <i class="iconfont icon-icon--1 replyInputBtn" @click="SubCommentReply($event)"></i>
      </div>
      <div class="report_curtain" v-show="report_curtain" @click="report_curtain=false;report=false;ReportComment=false"></div>
      <transition name="report">
        <div class="content_report" v-show="report">
          <span class="report_reason_title">
            <i class="iconfont icon-icon_comment"></i>
            选择举报理由
          </span>
          <ul class="report_reason" v-show="!commentShow">
            <li class="reason" @click="Report(1)">低俗与敏感内容</li>
            <li class="reason" @click="Report(2)">内容令人不适</li>
            <li class="reason" @click="Report(3)">偏离频道主题</li>
            <li class="reason" @click="Report(4)">垃圾.广告与欺诈</li>
            <li class="reason" @click="report=false;report_curtain=false">我再想想</li>
          </ul>
          <ul class="report_reason" v-show="commentShow">
            <li class="reason" @click="Report(5)">性骚扰</li>
            <li class="reason" @click="Report(6)">强烈冒犯性的内容</li>
            <li class="reason" @click="Report(4)">垃圾.广告与欺诈</li>
            <li class="reason" @click="report=false;report_curtain=false">我再想想</li>
          </ul>
        </div>
      </transition>
      <transition name="report">
        <div class="content_report" v-show="ReportComment">
          <span class="report_reason_title">
            <i class="iconfont icon-icon_comment"></i>
            你想要
          </span>
          <div class="comment_content">
            <span class="comment_c_name" :id="commentReport.commentId">
              <i class="iconfont icon-icon_comment"></i>
              {{commentReport.userName}}
            </span>
            <div class="comment_c_content">
              {{commentReport.userComment}}
            </div>
          </div>
          <ul class="report_reason">
            <li class="reason">复制评论</li>
            <li class="reason" @click="reportCommentFun(commentReport.commentId);commentShow=true">举报评论</li>
            <li class="reason" @click="ReportComment=false;report_curtain=false">我手滑了</li>
          </ul>
        </div>
      </transition>
      <div class="ts" v-show="tsShow"><span class="ts_c">{{ts}}</span></div>
    </div>
  </transition>
</template>

<script>
import SecretView from '@/components/SecretView/SecretView'
import { mapState } from 'vuex'
export default {
  name: 'Secret',
  data () {
    return {
      report: false,
      report_curtain: false,
      ReportComment: false,
      replyInput: '',
      showReplyInput: false,
      commentReplyData: {},
      commentReport: {},
      ReportCommentId: '',
      secret_data: {},
      commentShow: true,
      ts: '',
      tsShow: false
    }
  },
  computed: {
    ...mapState(['secret'])
  },
  methods: {
    JumpIndex () {
      this.$router.back(-1)
    },
    commentReportFun (comment) {
      // 长按800毫秒显示评论操作组件
      this.ReportComment = true
      this.report_curtain = true
      this.commentReport = comment
    },
    clearCommentReport () {
      clearInterval(this.Loop)
    },
    preloadImages (arr, callback) {
      // 已经加载完的图片数量
      let loadeImage = 0
      // 存放图片的数组
      let newImages = []
      // 处理存入字符串，取得src部分
      // let arr = str.match(/src="(.+)"\s{1}/g)

      // 返回一个promise对像
      return new Promise((resolve, reject) => {
        for (let i = 0; i < arr.length; i++) {
          for (let i = 0; i < arr.length; i++) {
            newImages[i] = new Image()
            // 设置图片src属性
            // newImages[i].src = arr[i].slice(5,-2)
            newImages[i].src = arr[i]
            // 图片绑定onload事件，确保加载完成
            newImages[i].onload = () => {
              loadeImage++
              // 当全部加载完成后，resove
              if (loadeImage === arr.length) {
                callback()
              }
            }
            newImages[i].onerror = () => {
              console.log(123)
            }
          }
        }
      })
    },
    GetScreen () {
      this.$axios.get('/api/secret.json', {
        params: {
          secret_id: this.secret.id || this.$route.params.SecretId,
          next_page: 20
        }
      }).then(this.getSecretInfoSucc)
    },
    getSecretInfoSucc (data) {
      let ResData = data.data
      this.secret_data = ResData.data
    },
    commentReplyInput (data) {
      // 显示评论框
      var comment = data.commentReplyData
      if (data.commentReplyData === undefined) {
        comment = {}
      }
      this.showReplyInput = data.showReplyInput
      this.commentReplyData = comment
      this.secretId = data.secretId
    },
    getCookie (name) {
      var strcookie = document.cookie
      var arrcookie = strcookie.split('; ')
      for (var i = 0; i < arrcookie.length; i++) {
        var arr = arrcookie[i].split('=')
        if (arr[0] === name) {
          return arr[1]
        }
      }
      return ''
    },
    SubCommentReply () {
      if (!this.replyInput) {
        this.showReplyInput = false
        this.replyInput = ''
        return
      }
      let params = this.$qs.stringify({
        'secretId': this.secret_data.id,
        'userComment': this.replyInput,
        'userDatetime': this.tool.getLocalTime(this.tool.currentTime()),
        'commentReplyId': this.commentReplyData.commentId || '',
        'X-CSRFToken': this.getCookie('csrftoken')
      })
      this.$axios.post('/api/commentReply.json', params).then(this.getSecretCommentSucc)
      this.replyInput = ''
      this.showReplyInput = false
      this.commentReplyData = {}
    },
    getSecretCommentSucc (data) {
      let ResData = data.data
      if (ResData.status === 'notLogin') {
        this.$router.push('/login')
        return
      }
      if (ResData.status === 'RefuseInteraction') {
        this.ts = ResData.msg
        this.tsShow = true
        return
      }
      this.secret_data.allcomment.push(ResData.data)
    },
    sameQuestion () {
      this.$axios.get('/api/sameQuestion.json', {
        params: {
          secretId: this.secret.id,
          datetime: this.tool.getLocalTime(this.tool.currentTime())
        }
      }).then(this.getSecretSameQuestionSucc)
    },
    getSecretSameQuestionSucc () {
      this.secret_data.likeNum += 1
    },
    reportCommentFun (id) {
      this.ReportComment = false
      this.report_curtain = true
      this.report = true
      this.ReportCommentId = id
    },
    Report (id) {
      this.$axios.get('/api/report.json', {
        params: {
          secretId: this.$route.params.SecretId,
          ReportReasonId: id,
          ReportCommentId: this.ReportCommentId,
          datetime: this.tool.getLocalTime(this.tool.currentTime())
        }
      }).then(this.getSecretReportSucc)
    },
    getSecretReportSucc (data) {
      if (data.data.status === 'notLogin') {
        this.$router.push('/login')
        return
      }
      this.report = false
      this.report_curtain = false
      this.ReportCommentId = ''
      this.ts = data.data.msg
      this.tsShow = true
      setTimeout(() => {
        this.tsShow = false
      }, 800)
    },
    length (obj) {
      if (obj === undefined) {
        return 0
      }
      return obj.length
    }
  },
  mounted () {
    this.$axios.interceptors.request.use((config) => {
      config.headers['X-Requested-With'] = 'XMLHttpRequest'
      let regex = /.*csrftoken=([^;.]*).*$/ // 用于从cookie中匹配 csrftoken值
      config.headers['X-CSRFToken'] = document.cookie.match(regex) === null ? null : document.cookie.match(regex)[1]
      return config
    })
    this.GetScreen()
  },
  destroyed () {
    this.secret_data = {}
  },
  components: {
    SecretView
  }
}
</script>

<style lang="stylus" scoped>
  .secret-enter-active, .secret-leave-active
    transition: all 0.3s
  .secret-enter, .secret-leave-to
    transform: translate3d(100%, 0, 0)
  .report-enter-active, .report-leave-active
    transition: all 0.3s
  .report-enter, .report-leave-to
    transform: translate3d(0, 100%, 0)
  .clearfloat:after
    display:block
    clear:both
    content:""
    visibility:hidden
    height:0
  .clearfloat
    zoom:1
  .secret_view
    z-index: 250
    position: absolute
    top: 0
    left: 0
    right: 0
    bottom: 0
    background-color: #fff
    display: flex
    flex-flow: column
    .secret_h
      position: relative
      z-index: 200
      height: 170px
      background-color: #006baa
      vertical-align: top
      .secret_h_return
        display: inline-block
        position: absolute
        top: 0
        bottom: 0
        left: 0
        i
          display: inline-block
          font-size: 70px
          color: #8ebfd9
          padding: 50px 0 50px 50px
      .secret_h_title
        text-align: center
        flex: 1
        h4
          line-height: 160px
          font-size: 50px
          color: #fffaf9
          font-weight: 800
      .secret_h_report
        display: inline-block
        position: absolute
        top: 0
        bottom: 0
        right: 0
        span
          display: inline-block
          font-size: 40px
          color: #fffaf9
          padding: 60px 50px 60px 0
    .secret_container
      flex: 1
      display: flex
      overflow: hidden
      padding: 0 48px
      background-color: #006baa
    .replyInput
      z-index: 300
      display: flex
      position: absolute
      left: 0
      right: 0
      bottom: 0
      background-color: rgba(0, 0, 0, 0.8)
      input
        flex: 1
        height: 140px
        background-color: rgba(0, 0, 0, 0)
        font-size: 40px
        color: #707372
        text-indent: 50px
      .replyInputBtn
        float: right
        display: inline-block
        font-size: 80px
        color: #b0b0b0
        padding: 30px 55px
        transform: rotate(45deg)
    .report_curtain
      z-index: 200
      position: absolute
      top: 0
      left: 0
      right: 0
      bottom: 0
      background-color: rgba(125, 125, 125, .6)
      filter: blur(15px)
      overflow:hidden
    .content_report
      z-index: 201
      position: absolute
      left: 0
      right: 0
      bottom: 0
      padding: 0 48px 0 45px
      .report_reason_title
        display: inline-block
        font-size: 40px
        color: #fdffff
        margin: 0 0 55px 0
        i
          font-size: 40px
          color: #fed42f
          margin-right: 10px
      .comment_content
        border-radius: 30px
        padding: 50px 50px 55px 55px
        background-color: rgba(210, 210, 210, 0.5)
        margin-bottom: 45px
        .comment_c_name
          display: inline-block
          font-size: 30px
          color: #fff
          i
            font-size: 36px
            color: #86d1f9
        .comment_c_content
          font-size: 40px
          line-height: 50px
          color: #fff
          margin: 30px 0 0 50px
      .report_reason
        float: right
        border-radius: 30px
        margin-bottom: 50px
        .reason
          line-height: 110px
          text-align: center
          background-color: rgba(255, 255, 255, 0.5)
          width: 770px
          height: 110px
          font-size: 40px
          color: #fff
          margin-bottom: 3px
          &:first-child
            border-top-right-radius: 30px
            border-top-left-radius: 30px
          &:last-child
            border-bottom-right-radius: 30px
            border-bottom-left-radius: 30px
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
