<template>
  <div id="app">
    <VideoPlayer></VideoPlayer>
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
