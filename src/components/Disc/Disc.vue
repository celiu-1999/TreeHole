<template>
  <transition name="slide">
    <secret-list></secret-list>
  </transition>
</template>

<script>
import SecretList from '@/components/SecretList/SecretList.vue'
export default {
  name: 'Disc',
  components: {
    SecretList
  }
}
</script>

<style lang="stylus" scoped>
  .slide-enter-active, .slide-leave-active
    transition: all 0.3s

  .slide-enter, .slide-leave-to
    transform: translate3d(100%, 0, 0)
</style>
