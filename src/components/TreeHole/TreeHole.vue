<template>
  <div class="tree_hole" ref="wrapper">
    <ul class="all_hole">
      <a class="hole" v-for="item in list" :key="item.id">{{item.name}}</a>
    </ul>
  </div>
</template>

<script>
import BScroll from 'better-scroll'
export default {
  name: 'TreeHole',
  props: {
    list: Array
  },
  mounted () {
    this.scroll = new BScroll(this.$refs.wrapper)
  },
  computed: {
  }
}
</script>

<style lang="stylus" scoped>
  .tree_hole
    width: 100%
    height: 100%
    padding-top: 25px
  .all_hole
    width: 100%
    padding: 0 0px 300px 0px
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
</style>
