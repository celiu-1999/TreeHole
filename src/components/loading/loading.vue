<template>
  <div class="loading">
    <img width="200" src="/static/img/svg-loaders/tail-spin.svg"><br>
  </div>
</template>

<script>
export default {
  props: {
    title: {
      type: String,
      default: '正在载入...'
    }
  }
}
</script>
<style scoped lang="stylus" rel="stylesheet/stylus">
  .loading
    display: flex;
    justify-content:center;
    align-items:Center;
    width: 100%
    height: 50%
    text-align: center
    img
      color: #000
</style>
