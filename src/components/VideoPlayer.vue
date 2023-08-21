<template>
  <div class="wropper" :style="{width: videoWidth}">
    <video 
    ref="player" width="720" 
    @mousemove="onMoveMause()"
    @click="playTogle()"
    @timeupdate="updateCurrentTime()" 
    @canplaythrough="getInnitParams()"
    >
    <source src="../assets/video/example-video.mp4" type="video/mp4">
    Your browser does not support the video tag.
  </video>
  <div class="control-panel" 
    v-if="totalTime != 0"
    @mouseenter="showControlPanel()"
    @mouseleave="hideControlPanel($event)"
    v-bind:class="{active: isPanelShow}">
    <div class="scroll-bar" ref="scrollBar"
      :style="{width: videoWidth}"
      @click="seek($event)">
      <div class="seek" :style="{width: seekWidth}"></div>
    </div>
    {{ getCurrentTime }} / {{ getTotalTime }} 
    <button @click="playTogle()">Play/Pause</button>
    <button @click="loop()">Loop</button>
    <button @click="mute()">Mute</button>
    <span>Speed: </span>
    <select v-model="speed" @change="speedChange()">
      <option value="1">1</option>
      <option value="1.25">1.25</option>
      <option value="1.5">1.5</option>
      <option value="1.75">1.75</option>
      <option value="2">2</option>
    </select>
    <input type="range" v-model="volume" @change="changeVolume()">
    </div>
    <div class="alert-play" v-bind:class="{active: isShowPlay}">
      <img src="../assets/icon/play_icon.svg">
    </div>
    <div class="alert-play"
      @click="playTogle()"
      v-bind:class="{active: isShowPause}">
      <img src="../assets/icon/pause_icon.svg">
    </div>
  </div>
</template>

<script>
export default {
  name: 'VideoPlayer',
  props: {
    
  },
  data () {
    return {
      totalTime: 0,
      currentTime: 0,
      seekWidth: '0px',
      videoWidth: '0px',
      speed: '1',
      volume: '50',
      isPlay: false,
      isShowPlay: false,
      isShowPause: false,
      isPanelShow: true,
      interval: setTimeout(() => {this.isPanelShow = false}, 3000),
    }
  },
  methods:{
    onMoveMause() {
      clearTimeout(this.interval)
      this.isPanelShow = true;
      this.interval = setTimeout(() => {this.isPanelShow = false}, 3000)
    },
    showControlPanel() {
      clearTimeout(this.interval)
      this.isPanelShow = true;
    },
    hideControlPanel (event) {
      if (event.fromElement.attributes[0].nodeValue.toString() != 'control-panel') {
        setTimeout(() => {this.isPanelShow = false}, 3000);
      }
    },
    changeVolume() {
      this.$refs.player.volume = this.volume/100
      console.log(this.volume/100)
    },
    speedChange() {
      console.log(this.speed)
      this.$refs.player.playbackRate = this.speed
    },
    seek (event) {
      let clickCoord = event.x
      let scroll = {
        startCoord: this.$refs.scrollBar.getBoundingClientRect().x,
        lengthScroll: this.$refs.scrollBar.getBoundingClientRect().width,
      }
      this.seekWidth = (clickCoord - scroll.startCoord)*100/scroll.lengthScroll + '%';
      let seekTime = ((clickCoord - scroll.startCoord) / scroll.lengthScroll) * this.totalTime;
      this.$refs.player.currentTime = seekTime;
    },
    mute() {
      this.$refs.player.mute = !this.$refs.player.mute;
      console.log('mute', this.$refs.player.mute)
    },
    loop () {
      this.$refs.player.loop = !this.$refs.player.loop;
      console.log('loop', this.$refs.player.loop)
    },
    getInnitParams() {
      this.videoWidth = this.$refs.player.width + 'px'
      this.$refs.player.volume = this.volume/100
      this.totalTime = this.$refs.player.duration
    },
    updateCurrentTime () {
      this.currentTime = this.$refs.player.currentTime
      this.seekWidth = (this.$refs.player.currentTime * 100 / this.$refs.player.duration) + '%';
    },
    playTogle () {
      this.isPlay = !this.isPlay;
      this.isPlay ? this.play() : this.pause()
    },
    pause () {
      this.isShowPause = true;
      this.$refs.player.pause()
    },
    play () {
      this.isShowPause = false;
      this.isShowPlay = true;
      this.$refs.player.play()
      setTimeout(() => {
        this.isShowPlay = false;
      }, 500);
    }
  },
  computed: {
    getTotalTime () {
      return new Date(this.totalTime*1000).toUTCString().split(/ /)[4]
    },
    getCurrentTime() {
      return new Date(this.currentTime*1000).toUTCString().split(/ /)[4]
    }
  },
  created () {
    
  }
}
</script>
