<template>
  <div class="home">
    <header-view :list="navList" :tree_list="TreeHoleList"></header-view>
    <!--<tree-hole-view :list="TreeHoleList"></tree-hole-view>-->
    <bottom-view></bottom-view>
    <router-view></router-view>
  </div>
</template>

<script>
import HeaderView from '@/components/Header/Header.vue'
import TreeHoleView from '@/components/TreeHole/TreeHole.vue'
import BottomView from '@/components/Bottom/Bottom.vue'
export default {
  name: 'Home',
  data () {
    return {
      navList: [],
      TreeHoleList: []
    }
  },
  methods: {
    get_index () {
      this.$axios.get('/api/index.json', {
        params: {
        }
      }).then(this.getHomeInfoSucc)
    },
    getHomeInfoSucc (res) {
      res = res.data
      console.log(res)
      this.navList = res.data.header
      this.TreeHoleList = res.data.tree_hole
    }
  },
  mounted () {
    this.get_index()
  },
  components: {
    HeaderView,
    TreeHoleView,
    BottomView
  }
}
</script>

<style lang="stylus" scoped>
 .home
   position: relative
   width: 100%
   height: 100%
</style>
