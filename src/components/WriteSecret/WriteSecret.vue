<template>
  <transition name="write">
    <div class="writeSecret">
      <div class="jar">{{jar.name}}</div>
      <div class="operation">
        <span class="operation_title">
          <i class="iconfont icon-icon_comment"></i>
          {{jar.ask}}
        </span>
        <ul class="operation_Option">
          <li class="option" @click="toInput">把{{jar.text}}装进罐头里</li>
          <li class="option" @click="JumpIndex">我再想想</li>
        </ul>
      </div>
      <router-view></router-view>
    </div>
  </transition>
</template>
<script>
import { mapState } from 'vuex'
export default {
  name: 'WriteSecret',
  data () {
    return {}
  },
  methods: {
    JumpIndex () {
      this.$router.back(-1)
    },
    toInput () {
      this.$router.push('/write/' + this.jar.id)
    }
  },
  computed: {
    ...mapState(['jar'])
  }
}
</script>

<style lang="stylus" scoped>
  .write-enter-active, .write-leave-active
    transition: all 0.3s
  .write-enter, .write-leave-to
    transform: translate3d(0, 100%, 0)
  .writeSecret
    z-index: 250
    position: absolute
    top: 0
    left: 0
    right: 0
    bottom: 0
    background: radial-gradient(circle, #1895cb, #138ec5, #097ab7)
    display: flex
    align-items:center
    justify-content: center
    .jar
      width: 300px
      height: 300px
      background: url("/static/img/shudong.png")
      font-size: 40px
      color: #fff
      line-height: 300px
      text-align: center
    .operation
      position: absolute
      left: 0
      right: 0
      bottom: 0
      padding: 0 48px 0 45px
      .operation_title
        display: inline-block
        font-size: 40px
        color: #fdffff
        margin: 0 0 55px 0
        i
          font-size: 40px
          color: #ffd429
          margin-right: 10px
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
</style>
