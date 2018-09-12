<template>
  <transition name="core">
    <div class="core_content">
      <div class="slide_downward">
        <span class="slide_d_center">
          <i class="iconfont icon-xiangxia- top"></i>
          <i class="iconfont icon-xiangxia- center"></i>
          <i class="iconfont icon-xiangxia- bottom"></i>
        </span>
      </div>
      <div class="user_navs">
        <router-link to="/user/news" class="user_nav">消息</router-link>
        <router-link to="/user/can" class="user_nav">罐头</router-link>
        <router-link to="/user/info" class="user_nav">设置</router-link>
      </div>
      <router-view></router-view>
    </div>
  </transition>
</template>

<script>
export default {
  name: 'UserCore'
}
</script>

<style lang="stylus" scoped>
  .core-enter-active, .core-leave-active
    transition: all 0.3s
  .core-enter, .core-leave-to
    transform: translate3d(100%, 0, 0)
  .core_content
    position: fixed
    top: 0
    left: 0
    right: 0
    bottom: 0
    display: flex
    flex-flow: column
    .slide_downward
      height: 100px
      padding: 20px 0 30px 0
      .slide_d_center
        position: relative
        display: block
        height: 80px
        width: 90px
        margin: 0px auto
        i
          font-size: 90px
          font-weight: 800
          color: #fff
        .top
          position: absolute
          top: 0
          opacity: .2
        .center
          position: absolute
          top: 30px
          opacity: .4
        .bottom
          position: absolute
          top: 60px
          opacity: .6
    .user_navs
      height: 140px
      display: flex
      padding: 50px 0 0 0
      justify-content:center
      .user_nav
        position: relative
        display: inline-block
        font-size: 50px
        color: #fff
        opacity: .4
        height: 90px
        font-weight: 600
        margin-left: 53px
        margin-right: 53px
      .user_nav.router-link-active
        color: #fff
        opacity: 1
        &::after
          content: " "
          position: absolute
          bottom: 0px
          left: 50%
          margin-left: -6px
          background-color: #fff
          width: 12px
          height: 12px
          border-radius: 50%
</style>
