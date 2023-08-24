<template>
  <div id="app">
    <VideoPlayer :width="'720'"></VideoPlayer>
  </div>
</template>

<script>
import VideoPlayer from './components/VideoPlayer.vue'

export default {
  name: 'App',
  components: {
     VideoPlayer
  }
}
</script>

<style lang="less">
@import './assets/style/index.less';
</style>
