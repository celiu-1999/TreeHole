<template>
  <transition name="input">
    <div class="WriteSecretInput">
      <header class="wsi_header clearfloat">
        <span class="wsi_h_l" @click="JumpIndex">
          <i class="iconfont icon-back"></i>
        </span>
        <span class="wsi_h_r" @click="complete">
          完成
        </span>
      </header>
      <div class="wsi_input">
        <div class="wsi_i_TimeWeather">
          <span class="wsi_i_T">{{dataTime}}</span>
          <span class="wsi_i_W">{{Weather}},{{Temperature}}</span>
        </div>
        <div class="wsi_i_Input">
          <textarea class="wsi_i_i_text" v-model="CanText" :placeholder="WSInput">
          </textarea>
        </div>
        <div class="wsi_i_imgs">
          <img :src="img" v-for="(img, index) of UploadImgs" @click="deleteImg(index)" :key="index">
          <span v-show="UploadImgs.length < 3" :alt="UploadImgs.length">
            <i class="iconfont icon-tupian"></i>
            <input class="file" name="file" type="file" accept="image/png,image/gif,image/jpeg" @change="update"/>
          </span>
        </div>
        <div class="wsi_i_WordInteraction">
          <span class="wsi_i_Word" v-show="!Word" @click="WordOption=true">#添加话题</span>
          <span class="Word" v-show="Word" @click="WordData={};Word=!Word">
            <i class="iconfont icon-close"></i>
            <span class="word_content">{{WordData.wordName}}</span>
          </span>
          <span class="wsi_i_Interaction" @click="Interaction = !Interaction">
            <span class="choice">
              <i :class="Interaction === true ? 'active' : ''"></i>
            </span>
            允许互动
          </span>
        </div>
      </div>
      <transition name="input">
        <div class="putIn" v-show="CanSub">
          <div class="putInBottom">
            <span class="operation_title op5" :class="DepositThrow == true ? 'active' : ''">
              <i class="iconfont icon-icon_comment"></i>
              你把情绪装进了罐头里...
            </span>
            <div class="gt op5" :class="DepositThrow == true ? 'active' : ''">
              <img src="/static/img/Jar.png" alt="">
              <span>{{WSInputData.name}}</span>
            </div>
            <ul class="operation_Option op5" :class="DepositThrow == true ? 'active' : ''">
              <li class="option" @click="DepositThrow=true;ShowSetName=true">将罐头掷入{{WSInputData.name}}之海</li>
              <li class="option">存为私密罐头</li>
            </ul>
            <div class="DepositThrow op5" v-show="DepositThrow" :class="DepositThrow == true ? 'active' : ''">
              将罐头掷入{{WSInputData.name}}之海
              <i class="iconfont icon-icon_comment"></i>
            </div>
            <div class="operation_title op5" v-show="ShowSetName" :class="SubOk == true ? 'active' : ''">
              <i class="iconfont icon-icon_comment"></i>
              <span>为此刻的自己取一个名字，每次装罐头都可以换新名字</span>
            </div>
            <div class="newName op5" v-show="ShowSetName" :class="SubOk == true ? 'active' : ''">
              <input type="text" v-model="WSInputName">
              <i class="iconfont icon-duihao1" @click="SubSecret"></i>
            </div>
            <span class="SubOk" v-show="SubOk">
              <i class="iconfont icon-icon_comment"></i>
              你的罐头消失在茫茫人海里
            </span>
            <ul class="SubOk operation_Option" v-show="SubOk">
              <li class="option" @click="toTreeHole">看看同样开心的人</li>
              <li class="option">返回首页</li>
            </ul>
          </div>
        </div>
      </transition>
      <transition name="word">
        <div class="word_option" v-show="WordOption">
          <div class="word_o_header">
            <span class="word_o_h_Close" @click="WordOption=!WordOption">
              <i class="iconfont icon-close"></i>
            </span>
            <span class="word_o_h_Title">选择话题</span>
          </div>
          <div class="word_o_ul">
            <li class="word_o_u_li" v-for="(word, index) of wordList" @click="ChoiceWord(word)" :key="index">{{word.wordName}}</li>
          </div>
        </div>
      </transition>
      <div class="ts" v-show="tsShow"><span class="ts_c">{{ts}}</span></div>
    </div>
  </transition>
</template>

<script>
import {mapMutations} from 'vuex'
export default {
  name: 'WriteSecretInput',
  data () {
    return {
      dataTime: '',
      Weather: '',
      Temperature: '',
      CanText: '',
      WordData: {},
      UploadImgs: [],
      wordList: [],
      WSInputData: {},
      WSInputName: '',
      WSInput: '',
      Interaction: true,
      Word: false,
      WordOption: false,
      CanSub: false,
      DepositThrow: false,
      ShowSetName: false,
      SubOk: false,
      files: [],
      tsShow: false,
      ts: ''
    }
  },
  methods: {
    JumpIndex () {
      this.$router.back(-1)
    },
    complete () {
      if (!this.CanText) {
        this.tsShow = true
        this.ts = '请输入不少于5字秘密'
        setTimeout(() => {
          this.tsShow = false
        }, 800)
        return
      }
      this.CanSub = true
    },
    toTreeHole () {
      let paramsId = this.$route.params.id
      this.$router.push('/')
      this.$router.push('/list/' + paramsId)
      this.setDisc({
        'id': this.WSInputData.id,
        'name': this.WSInputData.name
      })
    },
    update (e) {
      let file = e.target.files[0]
      let param = new FileReader()
      param.addEventListener('load', () => {
        this.UploadImgs.push(param.result)
      }, false)
      if (file) {
        param.readAsDataURL(file)
        this.files.push(file)
      }
    },
    ChoiceWord (word) {
      this.WordOption = false
      this.Word = true
      this.WordData = word
    },
    deleteImg (index) {
      this.UploadImgs.splice(index, 1)
    },
    GetJarInformation () {
      this.$axios.get('/api/JarInformation.json', {
        params: {
          TreeHole_id: this.$route.params.id
        }
      }).then(this.GetJarInformationInfoSucc)
    },
    GetJarInformationInfoSucc (data) {
      let ResData = data.data
      if (ResData.status === 'notLogin') {
        this.$router.push('/login')
        return
      }
      this.wordList = ResData.data.wordList
      this.WSInputData = ResData.data
      this.WSInputName = ResData.data.userName
      this.WSInput = ResData.data.input
    },
    SubSecret () {
      let formData = new FormData()
      for (var i = 0; i < this.files.length; i++) {
        formData.append('files', this.files[i])
      }
      formData.append('TreeHole_id', this.$route.params.id)
      formData.append('dataTime', this.dataTime)
      formData.append('Weather', this.Weather)
      formData.append('Temperature', this.Temperature)
      formData.append('CanText', this.CanText)
      formData.append('WordId', this.WordData.wordId)
      formData.append('WSInputName', this.WSInputName)
      formData.append('Interaction', Number(this.Interaction))
      let config = {
        headers: {
          'Content-Type': 'multipart/form-data'
        }
      }
      this.$axios.post('/api/SubSecret.json', formData, config).then(this.SubSecretInfoSucc)
      this.SubOk = true
    },
    SubSecretInfoSucc (data) {
      return 0
    },
    GetTime () {
      this.dataTime = this.tool.getLocalTime(this.tool.currentTime()) + ',' + this.tool.getWeek(this.tool.currentTime())
    },
    GetWeather () {
      let Weather = null
      this.$jsonp('http://api.map.baidu.com/telematics/v3/weather', {
        city: '北京',
        location: '北京',
        output: 'json',
        ak: 'H7W5CxI0BPzKtwGcBHmpGPAz50xP1Qjw',
        callback: 'www'
      }).then(json => {
        Weather = json.results[0].weather_data[0]
        this.Weather = Weather.weather
        this.Temperature = Weather.temperature
      }).catch(err => {
        console.log(err)
      })
    },
    GetIp () {
      this.$jsonp('http://pv.sohu.com/cityjson?ie=utf-8', null).then(this.GetIpSucc)
    },
    GetIpSucc (data) {
      console.log(data)
    },
    ...mapMutations({
      setDisc: 'SET_DISC'
    })
  },
  mounted () {
    this.GetJarInformation()
    // this.GetIp()
    this.GetWeather()
    this.GetTime()
  }
}
</script>

<style lang="stylus" scoped>
  .input-enter-active, .input-leave-active
    transition: all 0.3s
  .input-enter, .input-leave-to
    transform: translate3d(100%, 0, 0)
  .word-enter-active, .word-leave-active
    transition: all 0.3s
  .word-enter, .word-leave-to
    transform: translate3d(0, 100%, 0)
  .clearfloat:after
    display:block
    clear:both
    content:""
    visibility:hidden
    height:0
  .clearfloat
    zoom:1
  .WriteSecretInput
    z-index: 260
    position: fixed
    top: 0
    left: 0
    right: 0
    bottom: 0
    background: radial-gradient(circle, #1895cb, #138ec5, #097ab7)
    display: flex
    flex-flow: column
    .wsi_header
      height: 160px
      vertical-align: top
      span
        display:inline-block
      .wsi_h_l
        padding: 50px 0 50px 50px
        i
          font-size: 60px
          color: #94bdd4
      .wsi_h_r
        float: right
        padding: 55px 50px 55px 0
        font-size: 40px
        color: #fefbfc
    .wsi_input
      flex: 1
      margin: 0 48px
      padding: 48px
      background-color: #fff
      border-top-left-radius: 35px
      border-top-right-radius: 35px
      display: flex
      flex-flow: column
      .wsi_i_TimeWeather
        margin-bottom: 60px
        .wsi_i_T
          float: left
          font-size: 35px
          color: #a6c9d8
        .wsi_i_W
          float: right
          font-size: 35px
          color: #a6c9d8
      .wsi_i_Input
        flex: 1
        margin-bottom: 40px
        .wsi_i_i_text
          width: 100%
          height: 100%
          font-size: 45px
          line-height: 55px
          color: #115aa1
        textarea::-webkit-input-placeholder
          color: #dee6eb
      .wsi_i_imgs
        height: 190px
        vertical-align: top
        img
          float: left
          display: inline-block
          width: 190px
          height: 190px
          margin-right: 6px
        span
          float: left
          display: inline-block
          position: relative
          width: 190px
          height: 190px
          text-align: center
          background-color: #ebf8fc
          i
            z-index: 2
            font-size: 60px
            color: #97d8f5
            line-height: 190px
          .file
            z-index: 1
            position: absolute
            top: 0
            left: 0
            bottom: 0
            width: 100%
            opacity: 0
      .wsi_i_WordInteraction
        padding: 48px 0 0 0
        .wsi_i_Word
          font-size: 35px
          color: #a6cce4
          float: left
          vertical-align: top
        .Word
          display: inline-block
          i
            display: inline-block
            width: 50px
            height: 50px
            font-size: 20px
            color: #fffafc
            border-radius: 50%
            background-color: #049be5
            text-align: center
            line-height: 50px
            vertical-align: bottom
          span
            line-height: 45px
            margin-left: 10px
            font-size: 35px
            color: #00599b
            font-weight: 600
        .wsi_i_Interaction
          display: inline-block
          float: right
          vertical-align: top
          font-size: 35px
          line-height: 40px
          color: #a6cce4
          .choice
            display: inline-block
            width: 42px
            height: 42px
            border: 3px solid #a7cae3
            border-radius: 50%
            text-align: center
            vertical-align: bottom
            i
              display: inline-block
              margin: 9px
              width: 24px
              height: 24px
              border-radius: 50%
            i.active
              background-color: #00599d
    .putIn
      position: fixed
      top: 0
      left: 0
      right: 0
      bottom: 0
      background: radial-gradient(circle, #1895cb, #138ec5, #097ab7)
      display: flex
      flex-flow: column
      .putInBottom
        position: absolute
        left: 0
        right: 0
        bottom: 0
        padding: 0 48px 0 45px
        .op5
          opacity: 1
        .op5.active
          opacity: 0.5
        .operation_title
          display: inline-block
          font-size: 40px
          color: #fdffff
          margin: 0 0 55px 0
          i
            font-size: 40px
            color: #ffd429
            margin-right: 10px
        .gt
          position: relative
          text-align: center
          color: #1e8dc2
          margin-bottom: 60px
          img
            height: 238px
            text-align: center
          span
            position: absolute
            left: 50%
            bottom: 50%
            margin-bottom: -42px
            margin-left: -42px
            font-size: 42px
            font-weight: 600
            color: #1e8dc2
        .operation_Option
          float: right
          border-radius: 30px
          margin-bottom: 50px
          .option
            line-height: 110px
            text-align: center
            background-color: rgba(21, 119, 177, 0.6)
            width: 770px
            height: 110px
            font-size: 40px
            color: #e6ffff
            margin-bottom: 3px
            &:first-child
              border-top-right-radius: 30px
              border-top-left-radius: 30px
            &:last-child
              border-bottom-right-radius: 30px
              border-bottom-left-radius: 30px
        .DepositThrow
          display: inline-block
          width: 100%
          text-align: right
          font-size: 40px
          color: #fdffff
          margin: 0 0 55px 0
          i
            font-size: 40px
            color: #b0d0e6
            margin-left: 10px
        .operation_title
          display: inline-block
          margin: 0 0 55px 0
          i
            display: inline-block
            font-size: 40px
            color: #ffd429
            margin-right: 10px
            vertical-align: top
          span
            display: inline-block
            font-size: 40px
            line-height: 45px
            color: #fdffff
            width: 910px
        .newName
          text-align: right
          margin: 55px 0
          input
            height: 50px
            line-height: 50px
            font-size: 40px
            color: #fff
            border: none
            background: rgba(0, 0, 0, 0)
            text-align: right
            margin-right: 40px
            vertical-align: bottom
          i
            display: inline-block
            width: 48px
            height: 48px
            line-height: 50px
            background-color: #fdffff
            border-radius: 50%
            text-align: center
            font-size: 25px
            color: #006fa8
            vertical-align: bottom
        .SubOk
          display: inline-block
          font-size: 40px
          color: #fdffff
          margin: 0 0 55px 0
          i
            font-size: 40px
            color: #ffd429
            margin-right: 10px
    .word_option
      position: fixed
      top: 0
      left: 0
      right: 0
      bottom: 0
      background: radial-gradient(circle, #1895cb, #138ec5, #097ab7)
      display: flex
      flex-flow: column
      .word_o_header
        position: relative
        display: flex
        height: 160px
        .word_o_h_Close
          position: absolute
          left: 0
          top: 0
          bottom: 0
          display: inline-block
          padding: 60px
          i
            font-size: 40px
            color: #fff
        .word_o_h_Title
          flex: 1
          text-align: center
          font-size: 48px
          color: #fff
          font-weight: 600
          line-height: 160px
      .word_o_ul
        padding: 0 50px
        .word_o_u_li
          font-size: 40px
          line-height: 130px
          color: #fff
          list-style-type: none
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
