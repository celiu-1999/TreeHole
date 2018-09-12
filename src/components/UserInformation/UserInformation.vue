<template>
  <div class="user_info">
    <ul class="all_opation">
      <li class="opation user_data clearfloat">
        <span class="opation_l">个人资料</span>
        <span class="opation_r">{{years}}后,&nbsp;&nbsp;<span v-if="sex==0">女</span><span v-if="sex==1">男</span></span>
      </li>
      <li class="opation comment_name clearfloat">
        <span class="opation_l">设置评论昵称</span>
        <span class="opation_r" @click="serName" v-show="!setName">
          {{comment_name}}
          <i class="iconfont icon-more"></i>
        </span>
        <span class="opation_r" v-show="setName">
          <input type="text" v-model="comment_name">
          <i class="iconfont icon-duihao1" @click="setNameSub"></i>
        </span>
      </li>
      <li class="opation signOut">
        <span class="opation_l">退出登录</span>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'UserInformation',
  data () {
    return {
      setName: false,
      comment_name: '',
      years: '',
      sex: 1
    }
  },
  methods: {
    serName () {
      this.setName = true
    },
    getUserInfo () {
      this.$axios.get('/user/getUserInfo.json', {
        params: {}
      }).then(this.getUserInfoSucc)
    },
    getUserInfoSucc (data) {
      let Resdata = data.data
      if (Resdata.status === 'notLogin') {
        this.$router.push('/login')
        return
      }
      this.comment_name = Resdata.data.name
      this.years = Resdata.data.years
      this.sex = Resdata.data.sex
    },
    setNameSub () {
      this.$axios.get('/user/setName.json', {
        params: {
          name: this.comment_name
        }
      }).then(this.setNameSubSucc)
    },
    setNameSubSucc (data) {
      // let Resdata = data.data
      this.setName = false
    }
  },
  mounted () {
    this.getUserInfo()
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
  .user_info
    flex: 1
    padding: 0 50px
    .all_opation
      li
        display: block
        font-size: 40px
        color: #fff
        line-height: 150px
        margin-bottom: 20px
        .opation_l
          float: left
        .opation_r
          float: right
          i
            display: inline-block
            margin-left: 5px
            font-size: 25px
            color: #000
            opacity: .8
          input
            width: 300px
            text-align: right
            color: #fff
            font-size: 38px
            line-height: 150px
            background-color: rgba(0, 0, 0, 0)
      .comment_name
        margin-bottom: 30px
      .signOut
        border-top: 3px solid #3394c6
</style>
