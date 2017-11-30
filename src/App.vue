<template>
  <div id="app">
    <m-header></m-header>
    <tab></tab>
    <!--切换tab时保存线程-->
    <keep-alive>
      <router-view></router-view>
    </keep-alive>
  </div>
</template>

<script type="text/ecmascript-6">
  import MHeader from 'components/m-header/m-header.vue';
  import Tab     from 'components/tab/tab.vue'

  export default {
    components:{
      MHeader,
      Tab
    }
  }
</script>

<style scoped lang="stylus"ref="stylesheet/stylus">
</style>
