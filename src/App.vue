<style lang="stylus" rel="stylesheet/stylus">
.mainApp
  margin 0 0
  position absolute
  height 100%
  width 100%
  overflow hidden
</style>

<template>
<div class="mainApp">
  <router-view class="router-view"></router-view>
</div>

</template>

<script>

export default {
  data() {
    return {
      seller: {}
    }
  },
  created() {}
}

</script>
