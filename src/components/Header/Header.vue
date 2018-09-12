<template>
  <div class="content">
    <div class="header">
      <header class="header_center">
        <ul class="all_column">
          <li class="column" slot="pagination" :class="{'active': active == item.id}" @click="jump(index, item.id)" v-for="(item, index) of tree_list" :key="item.id">{{item.title}}</li>
        </ul>
      </header>
    </div>
    <div class="tree_hole wrapper" ref="wrapper">
      <swiper :options="swiperOption" class="swiperOption scrollTop"  ref="swiperTop">
        <swiper-slide class="all_hole" v-for="item of tree_list" :key="item.id" :alt="item.title">
          <ul class="all_hole">
            <li @click="selectItem(hole)" class="hole" v-for="hole in item.hole" :key="hole.id">{{hole.name}}</li>
          </ul>
        </swiper-slide>
      </swiper>
    </div>
  </div>
</template>

<script>
import BScroll from 'better-scroll'
import {mapMutations} from 'vuex'
export default {
  name: 'Header',
  props: {
    list: Array,
    tree_list: Array
  },
  mounted () {
    this.scroll = new BScroll(this.$refs.wrapper, {
      click: true
    })
  },
  data () {
    return {
      swiperOption: {
        onSlideChangeEnd: swiper => {
          let scrollTop = document.getElementsByClassName('scrollTop')[0]
          scrollTop.setAttribute('style', 'transform: translate(0px, -25px) scale(1) translateZ(0px); !important')
          // scrollTop.style.transform = 'translate(0px, -25px) scale(1) translateZ(0px)'
          this.active = '0000' + (swiper.realIndex + 1)
        }
      },
      active: '00001',
      trans_num: []
    }
  },
  computed: {
    showSwiper () {
      return this.tree_list.length
    },
    swiperTop () {
      return this.$refs.swiperTop.swiper
    }
  },
  methods: {
    jump (num, id) {
      let scrollTop = document.getElementsByClassName('scrollTop')[0]
      // let translate = scrollTop.style.transform
      // // 获取 translate 高度
      // var TranslateNum = translate.replace(/[^0-9]/ig, '')
      // TranslateNum = TranslateNum.substring(0, TranslateNum.length - 2).substr(1)
      // this.trans_num = []
      // this.rec_num(TranslateNum)
      // // 动态改变 transform: translate 高度
      // this.trans_num.forEach((item, index) => {
      //   setTimeout(() => {
      //     scrollTop.setAttribute('style', 'transform: translate(0px, ' + item + 'px) scale(1) translateZ(0px); !important')
      //     console.log(scrollTop.style.transform)
      //   }, 500)
      // })
      scrollTop.style.transform = 'translate(0px, -25px) scale(1) translateZ(0px)'
      this.swiperTop.slideTo(num, 500, false)
      document.documentElement.scrollTop = 0
      this.active = id
    },
    rec_num (num) {
      if (num <= -25) {
        return -25
      }
      this.trans_num.push(num - 10)
      return this.rec_num(num - 10)
    },
    selectItem (hole) {
      this.$router.push({
        path: `/list/${hole.id}`
      })
      this.setDisc(hole)
    },
    ...mapMutations({
      setDisc: 'SET_DISC'
    })
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="stylus" scoped>
  .content
    height: 100%
  .header_center
    width: 100%
  .all_column
    height: 205px
    width: 100%
    padding: 0 2.2%
    .column
      vertical-align: top
      display: inline-block
      width: 19%
      line-height: 205px
      text-align: center
      font-size: 48px
      color: #4a95bf
    .column.active
      color: #fffcfd
      position relative
    .column.active:after
      content:" ";
      position: absolute
      bottom: 40px
      left: 50%
      margin-left: -6px
      background-color: #fff
      width: 12px
      height: 12px
      border-radius: 50%
  .tree_hole
    width: 100%
    height: 100%
    overflow: hidden
    padding-top: 25px
  .all_hole
    width: 100%
    padding: 0 0px 200px 0px
  .hole
    background: url("../../../static/img/shudong.png")
    display: inline-block
    width: 300px
    height: 300px
    color: #fff
    line-height: 300px
    text-align: center
    font-size: 40px
    margin: 0 28px 60px 28px
  .swiper-img
    width: 100%
    background-color: #fff
  .wrapper
    overflow: hidden
    width: 100%
    height: 100%
</style>
