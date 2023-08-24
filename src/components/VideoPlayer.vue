<template>
  <div v-show="totalTime != 0" class="wropper" v-bind:class="{full: isFull}" :style="{width: widthInnit + 'px'}">
    <video 
      ref="player" :width="widthInnit" 
      @mousemove="onMoveMause()"
      @click="playTogle()"
      @timeupdate="updateCurrentTime()" 
      @canplaythrough="getInnitParams()"
      >
      <source src="../assets/video/video.mp4" type="video/mp4">
      Your browser does not support the video tag.
    </video>
    <div class="control-panel" 
      v-if="totalTime != 0"
      @mouseenter="showControlPanel()"
      @mouseleave="hideControlPanel($event)"
      v-bind:class="{active: isPanelShow}">
      <div class="scroll-bar" ref="scrollBar"
        :style="{width: widthInnit + 'px'}"
        @click="seek($event)">
        <div class="seek" :style="{width: timeMarker}"></div>
      </div>
      <div class="control-panel-button">
        <div class="buttons">
          <button class="play btn" @click="playTogle()">
            <img v-if="!isPlay" src="../assets/icon/circle_play_btn.svg" alt="Play">
            <img v-if="isPlay" src="../assets/icon/circle_pause_btn.svg" alt="Pause">
          </button>
          <button @click="loop()" class="loop btn" v-bind:class="{active: $refs.player.loop}">
            <img src="../assets/icon/loop_btn.svg" alt="Loop">
          </button>
          <button @click="mute()" class="mute btn" v-bind:class="{active: $refs.player.muted}">
            <img src="../assets/icon/mute_btn.svg" alt="Mute">
          </button>
					<div class="volume-panel">
						<div>Volume</div>
						<input class="volume" type="range" v-model="volume" @change="changeVolume()">
					</div>
        </div>
        <div class="time-bar">
          <div class="speed-panel">
          <span class="speed-title">Speed: </span>
          <select class="speed-select" v-model="speed" @change="speedChange()">
            <option value="1">1</option>
            <option value="1.25">1.25</option>
            <option value="1.5">1.5</option>
            <option value="1.75">1.75</option>
            <option value="2">2</option>
          </select>
          </div>
          <div class="control-panel-time">
          {{ getCurrentTime }} / {{ getTotalTime }} 
          </div>
					<button @click="fullscreenVideo()" class="fullscreen btn">
						<img v-if="!isFull" src="../assets/icon/fullscreen.svg" alt="fullscreen">
						<img v-if="isFull" src="../assets/icon/fullscreen_return.svg" alt="fullscreen">
					</button>
        </div>
      </div>
    </div>
    <div class="alert-play" 
      @click="playTogle()"
      v-bind:class="{active: isShowPlay}">
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
    width: String
  },
  data () {
    return {
      totalTime: 0,
      currentTime: 0,
      timeMarker: '0px',
      speed: '1',
      volume: '50',
      isPlay: false,
      isShowPlay: true,
      isShowPause: false,
      isPanelShow: true,
      interval: setTimeout(() => {this.isPanelShow = false}, 3000),
			widthInnit: '',
			isFull: false
    }
  },
  methods:{
		fullscreenVideo() {
			this.isFull = !this.isFull;
			if (this.isFull) {
				this.widthInnit = window.innerWidth
			} else {
				this.widthInnit = this.width;
			}
			console.log(this.$refs.player.width)
		},
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
        this.interval = setTimeout(() => {this.isPanelShow = false}, 3000)
      }
    },
    changeVolume() {
      this.$refs.player.volume = this.volume/100
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
      this.timeMarker = (clickCoord - scroll.startCoord)*100/scroll.lengthScroll + '%';
      let seekTime = ((clickCoord - scroll.startCoord) / scroll.lengthScroll) * this.totalTime;
      this.$refs.player.currentTime = seekTime;
    },
    mute() {
      this.$refs.player.muted = !this.$refs.player.muted;
      console.log('mute', this.$refs.player.mute)
    },
    loop () {
      this.$refs.player.loop = !this.$refs.player.loop;
      console.log('loop', this.$refs.player.loop)
    },
    getInnitParams() {
			this.widthInnit = this.width;
      this.$refs.player.volume = this.volume/100
      this.totalTime = this.$refs.player.duration
    },
    updateCurrentTime () {
      this.currentTime = this.$refs.player.currentTime
      this.timeMarker = (this.$refs.player.currentTime * 100 / this.$refs.player.duration) + '%';
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
