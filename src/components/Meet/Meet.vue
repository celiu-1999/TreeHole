<template>
  <transition name="slide">
    <div class="meet">
      <header class="meet_header clearfloat">
        <span class="iconfont icon-close meet_l" @click="JumpIndex"></span>
        <span class="meet_c meet_t"><i ref="meet_t_x" class="meet_t_s">此刻之海</i></span>
        <span class="meet_r">举报</span>
      </header>
      <div class="meetSecret">
        <swiper :options="swiperOption" ref="MeetSwiperTop" class="secretBroadcast">
          <swiper-slide class="all_Secret" v-for="secret_data of meet_data" :key="secret_data.id">
            <div class="singleSecret">
              <secret-view :ref="'view' + secret_data.id" @commentReportFun="commentReportFun" @commentReply="commentReplyInput" :secret_data="secret_data"></secret-view>
            </div>
          </swiper-slide>
        </swiper>
      </div>
      <div class="replyInput" v-show="showReplyInput">
        <input type="text" placeholder="回复" v-model="replyInput" @blur="showReplyInput=false">
        <i class="iconfont icon-icon--1 replyInputBtn" @click="SubCommentReply()"></i>
      </div>
      <div class="report_curtain" v-show="report_curtain" @click="report_curtain=false;report=false;ReportComment=false"></div>
      <transition name="report">
        <div class="content_report" v-show="report">
          <span class="report_reason_title">
            <i class="iconfont icon-icon_comment"></i>
            选择举报理由
          </span>
          <ul class="report_reason">
            <li class="reason" @click="Report(0)">低俗不堪</li>
            <li class="reason" @click="Report(1)">内容令人不适</li>
            <li class="reason" @click="Report(2)">这也太水了吧</li>
            <li class="reason" @click="Report(3)">垃圾广告</li>
            <li class="reason" @click="Report(4)">政治敏感</li>
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
            <li class="reason" @click="reportCommentFun(commentReport.commentId)">举报评论</li>
            <li class="reason" @click="ReportComment=false;report_curtain=false">我手滑了</li>
          </ul>
        </div>
      </transition>
    </div>
  </transition>
</template>

<script>
import SecretView from '@/components/SecretView/SecretView.vue'
export default {
  name: 'Meet',
  data () {
    return {
      swiperOption: {
        loop: false
      },
      meet_data: [],
      report: false,
      report_curtain: false,
      ReportComment: false,
      replyInput: '',
      showReplyInput: false,
      commentReplyData: {},
      commentReport: {},
      ReportCommentId: '',
      secretId: '',
      next_page: 3
    }
  },
  methods: {
    JumpIndex () {
      this.$router.back(-1)
    },
    commentReportFun (comment, secretId) {
      // 长按800毫秒显示评论操作组件
      this.ReportComment = true
      this.report_curtain = true
      this.commentReport = comment
      this.secretId = secretId
    },
    GetMeet () {
      // 获取Meet组件数据
      this.$axios.get('/api/allMeet.json', {
        params: {
          next_page: this.next_page
        }
      }).then(this.getMeetInfoSucc)
    },
    getMeetInfoSucc (data) {
      // 处理Meet组件数据
      let ResData = data.data
      this.meet_data = ResData.data
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
    SubCommentReply () {
      // 提交评论或回复
      this.$axios.get('/api/commentReply.json', {
        params: {
          secretId: this.secretId,
          userComment: this.replyInput,
          commentReplyId: this.commentReplyData.commentId,
          userDatetime: this.tool.getLocalTime(this.tool.currentTime())
        }
      }).then(this.getSecretCommentSucc)
      this.replyInput = ''
      this.showReplyInput = false
      this.commentReplyData = {}
    },
    getSecretCommentSucc (data) {
      let ResData = data.data
      let a = this.$refs['view' + this.secretId][0]
      a.addComment(ResData.data)
    },
    reportCommentFun (id, secretId) {
      this.ReportComment = false
      this.report_curtain = true
      this.report = true
      this.ReportCommentId = id
    },
    Report (id) {
      this.$axios.get('/api/report.json', {
        params: {
          secretId: this.secretId,
          ReportReasonId: id,
          ReportCommentId: this.ReportCommentId,
          datetime: this.getLocalTime(this.currentTime())
        }
      }).then(this.getSecretReportSucc)
    },
    getSecretReportSucc (data) {
      this.report = false
      this.report_curtain = false
      this.ReportCommentId = ''
    },
    length (obj) {
      if (obj === undefined) {
        return 0
      }
      return obj.length
    }
  },
  mounted () {
    this.GetMeet()
    // this.scroll = new BScroll(this.$refs.meet, {
    //   click: true
    // })
  },
  destroyed () {
    this.meet_data = {}
  },
  components: {
    SecretView
  }
}
</script>

<style lang="stylus" scoped>
  .slide-enter-active, .slide-leave-active
    transition: all 0.3s
  .slide-enter, .slide-leave-to
    transform: translate3d(100%, 0, 0)
  .report-enter-active, .report-leave-active
    transition: all 0.3s
  .report-enter, .report-leave-to
    transform: translate3d(0, 100%, 0)
  .meet
    position: fixed
    z-index: 100
    top: 0
    left: 0
    bottom: 0
    right: 0
    background: #fff
    display: flex
    flex-flow: column
    .meet_header
      display: flex
      height: 170px
      background-color: #006baa
      .meet_l
        z-index: 10
        display: inline-block
        font-size: 50px
        padding: 60px 60px 60px 60px
        color: #fffef6
      .meet_c
        flex: 1
        font-size: 50px
        color: #fffef6
        display: inline-block
        line-height: 170px
        text-align: center
        .meet_t_s
          display: inline-block
      .meet_r
        z-index: 10
        font-size: 45px
        color: #fffef6
        float: right
        font-weight: 300
        padding: 62px 50px 62px 0
  .meetSecret
    flex: 1
    overflow: hidden
    padding-bottom: 96px
    background-color: #006baa
    .secretBroadcast
      height: 100%
      .all_Secret
        .singleSecret
          display: flex
          height: 100%
          padding: 0 48px 0px 48px
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
</style>
