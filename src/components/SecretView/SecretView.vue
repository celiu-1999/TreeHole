<template>
  <div class="secret">
    <div class="secret_header">
      <span class="day">{{tool.getDay(secret_data.datetime)}}</span>
      <span class="date_weather">
    <span class="date">
      {{tool.getYearMonth(secret_data.datetime)}},{{tool.getWeek(secret_data.datetime)}}
    </span>
    <span class="weather">
      {{secret_data.weather}},{{secret_data.temperature}}
    </span>
    </span>
      <span class="meanwhile_ask">{{secret_data.likeNum}} {{secret_data.likeName}}</span>
    </div>
    <div :ref="secret_data.id" class="secret_subject">
      <div class="secret_subject_c">
        <div class="secret_name">
          <span> {{ secret_data.sex == 0 ? '她' : '他' }}: {{secret_data.name}}</span>
        </div>
        <div class="secret_writing">
          <div class="secret_w_text">
            <i class="word" v-show="secret_data.word != ''">#{{secret_data.word}}</i>{{secret_data.content}}
          </div>
          <div class="secret_w_imgs">
            <img :src="img" v-for="(img, index) of secret_data.imgs" v-bind:key="index">
          </div>
        </div>
        <div class="secret_comment" v-show="secret_data.allcomment.length">
          <div class="secret_comment_title">
            评论 {{length(secret_data.allcomment)}}
          </div>
          <ul class="secret_all_comment">
            <li class="comment" v-for="comment of secret_data.allcomment" @touchstart="commentReportFun(comment)" @touchend="clearCommentReport" v-bind:key="comment.commentId">
              <div class="comment_h">
                <i class="iconfont icon-icon_comment" :class="comment.sex == 1 ? 'nan' : 'nv'"></i>
                <span>{{comment.userName}}</span>
                <span v-if="JSON.stringify(comment.commentReply) !== '{}'">回复</span>
                <span v-if="JSON.stringify(comment.commentReply) !== '{}'" :key="comment.commentReply.commentId" :UserId="comment.commentReply.userId">{{comment.commentReply.userName}}</span>
              </div>
              <div class="comment_c">
                {{comment.userComment}}
              </div>
              <div class="comment_user">
                <span class="comment_r_date">{{tool.SetDatetime(comment.userDatetime)}}</span>
                <span class="comment_r_reply" @click="commentReplyInput(comment)">回复</span>
                <span class="comment_r_chat">私聊</span>
              </div>
            </li>
          </ul>
        </div>
      </div>
    </div>
    <div class="secret_bottom_column">
    <span class="secret_b_c_c_c secret_b_c_chat">
      <div class="clearfloat">
        <i class="iconfont icon-icon--"></i>
        <span>私聊</span>
      </div>
    </span>
      <span class="secret_b_c_c_c secret_b_c_comment clearfloat">
      <div class="clearfloat" @click="commentReplyInput(undefined)">
        <i class="iconfont icon-icon_comment"></i>
        <span class="">评论</span>
      </div>
    </span>
      <span class="secret_b_c_c_c secret_b_c_ask clearfloat">
      <div class="clearfloat" @click="sameQuestion">
        <i class="iconfont icon-bqxin" :class="secret_data.userLike == 1 ? 'yes' : 'no'"></i>
        <span>{{secret_data.likeName}}</span>
      </div>
    </span>
    </div>
  </div>
</template>

<script>
import BScroll from 'better-scroll'
export default {
  name: 'SecretView',
  props: {
    secret_data: Object
  },
  data () {
    return {
      swiperOption: {
        loop: false
      },
      meet_data: [],
      report: false,
      replyInput: '',
      showReplyInput: false,
      commentReplyData: {},
      commentReport: {},
      ReportCommentId: ''
    }
  },
  watch: {
    secret_data () {
      setTimeout(() => {
        this.scroll.refresh()
      }, 200)
    }
  },
  methods: {
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
    commentReportFun (comment) {
      this.Loop = setTimeout(() => {
        this.$emit('commentReportFun', comment, this.secret_data.id)
      }, 500)
    },
    clearCommentReport () {
      clearInterval(this.Loop)
    },
    commentReplyInput (comment) {
      if (comment === undefined) {
        comment = {}
      }
      this.$emit('commentReply', {
        showReplyInput: true,
        commentReplyData: comment,
        secretId: this.secret_data.id
      })
    },
    addComment (data) {
      this.secret_data.allcomment.push(data)
      setTimeout(() => {
        this.scroll.refresh()
      }, 200)
    },
    sameQuestion () {
      this.$axios.get('/api/sameQuestion.json', {
        params: {
          secretId: this.secret_data.id,
          datetime: this.tool.getLocalTime(this.tool.currentTime())
        }
      }).then(this.getSecretSameQuestionSucc)
    },
    getSecretSameQuestionSucc (data) {
      if (data.data.status === 'notLogin') {
        this.$router.push('/login')
        return
      }
      this.secret_data.userLike = this.secret_data.userLike === 1 ? 0 : 1
      this.secret_data.likeNum += 1
    },
    length (obj) {
      if (obj === undefined) {
        return 0
      }
      return obj.length
    }
  },
  mounted () {
    this.preloadImages(this.secret_data.imgs, () => {
      this.scroll = new BScroll(this.$refs[this.secret_data.id], {
        click: true
      })
    })
  }
}
</script>

<style lang="stylus" scoped>
  .secret
    flex: 1
    position: relative
    overflow: hidden
    padding: 80px 50px 0 50px
    background-color: #fff
    border-radius: 30px
    vertical-align: top
    .secret_header
      margin-bottom: 50px
      .day
        display: inline-block
        height: 100px
        font-size: 100px
        color: #2a9ce2
        line-height: 100px
        margin-right: 25px
      .date_weather
        display: inline-block
        width: 450px
        .date, .weather
          display: block
          font-size: 35px
          line-height: 50px
          color: #accadd
      .meanwhile_ask
        float: right
        margin: 40px 0 0 0
        display: inline-block
        font-size: 35px
        color: #a9c8d5
    .secret_subject
      height: 82%
      overflow: hidden
      .secret_subject_c
        padding-bottom: 20px
    .secret_name
      span
        font-family: 'FangSong'
        font-size: 50px
        color: #3e97ce
        font-weight: 200
    .secret_writing
      margin-top: 50px
      .secret_w_text
        line-height: 60px
        font-size: 45px
        color: #215b96
        .word
          font-weight: 600
          color: #089bd9
          margin-right: 10px
      .secret_w_imgs
        margin-top: 100px
        img
          width: 100%
    .secret_comment
      margin-top: 110px
      .secret_comment_title
        font-size: 45px
        color: #155283
        font-weight: 800
        margin-bottom: 60px
      .secret_all_comment
        .comment
          margin-bottom: 60px
          .comment_h
            i
              font-size: 36px
              margin-right: 20px
            i.nv
              color: #fdc9c8
            i.nan
              color: #81d3fe
            span
              font-size: 30px
              color: #035695
              font-weight: 800
              margin-right: 15px
          .comment_c
            margin: 20px 0 0 50px
            line-height: 60px
            font-size: 40px
            color: #035695
          .comment_user
            margin: 40px 0 0 50px
            .comment_r_date
              font-size: 35px
              color: #6292b5
            .comment_r_reply
              font-size: 35px
              color: #349cca
              margin: 0 30px 0 30px
            .comment_r_chat
              font-size: 35px
              color: #349cca
    .secret_bottom_column
      position: absolute
      bottom: 0px
      left: 0
      right: 0
      height: 130px
      background-color: #f4f9fa
      .secret_b_c_c_c
        display: inline-block
        width: 33%
        text-align: center
        line-height: 130px
        vertical-align: top
        div
          width: 165px
          margin: 0 auto
          i
            font-size: 66px
            color: #a2d6f4
            float: left
          span
            float: left
            margin-left: 15px
            height: 130px
            font-size: 35px
            color: #aac7d8
            line-height: 130px
      .secret_b_c_chat
        div
          i
            font-size: 78px
      .secret_b_c_ask
        div
          i
            font-size: 57px
          i.yes
            color: #fdc9c8
          i.no
            color: #a2d6f4
</style>
