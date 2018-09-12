<template>
  <div class="Bottom">
    <router-link to="meet" class="b_btn_x btn_left" >
      <i class="iconfont icon-xiaolianmanyifuwu"></i>
      <span>遇见</span>
    </router-link>
    <router-link to="/user/news" class="b_btn_d btn_center">
      <i class="iconfont icon-nb-"></i>
    </router-link>
    <div class="b_btn_x btn_right" @click="toWrite">
      <i class="iconfont icon-biji"></i>
      <span>此刻</span>
    </div>
  </div>
</template>

<script>
import {mapMutations} from 'vuex'
export default {
  name: 'Bottom',
  methods: {
    toWrite () {
      this.$router.push('write')
      this.set_jar({
        id: 10,
        name: '此刻',
        ask: '此刻你想说点什么?',
        text: '此刻的情绪'
      })
    },
    ...mapMutations({
      set_jar: 'SET_JAR'
    })
  }
}
</script>

<style lang="stylus" scoped>
  .Bottom
    z-index: 100
    height: 260px
    padding: 0 100px
    position: absolute
    bottom: 0
    left: 0
    right: 0
    vertical-align: top
    background: linear-gradient(rgba( 0, 0, 0, 0), #015c9e);
    .b_btn_x
      display: inline-block
      padding: 45px 0 0 0
      height: 100%
      text-align: center
      i
        font-size: 70px
        color: #cde1ed
        padding: 0 90px 20px 90px
        display: block
      span
        font-size: 35px
        color: #f9ffff
        text-align: center
    .b_btn_d
      display: inline-block
      i
        font-size: 200px
        width: 200px
        height: 200px
        padding: 0 86px 0 86px
        color: #b7d1e3
    .btn_right
      width: 250px
      i
        font-size: 100px
        padding: 0 70px 0px 70px
</style>
