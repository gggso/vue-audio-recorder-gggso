<style lang="scss" scoped>
  @import '../scss/icons';
  .mini-player {
    width: 190px;
    height: 39px;
    border-radius: 3px;
    align-items: center;
    justify-content: center;
    background:none ;
    font-family: 'Roboto', sans-serif;
    position: relative;
    overflow: hidden;
    &-bar {
      height: 39px;
      position: absolute;
      top: 0;
      width: 100%;
    }

    &-actions {
      width: 100%;
      position: absolute;
      z-index: 20;
      height:100%;
    }

    &__progress {
      width: 100%;
      height:100%;
      background:none;
    }

    &__time {
      color: #515a6e;
      font-size: 16px;
      position: absolute;
      left: 20px;
      top: 8px;
      z-index: 19
    }

    &__play {
      width: 32px;
      height: 32px;
      background-color: #FFFFFF;
      box-shadow: 0 2px 11px 4px rgba(0,0,0,0.07);
      border: 0;
      padding: 0;
      position: absolute;
      top: 3px;
      left: 50%;
      margin-left: -16px;
      &--active {
        fill: white !important;
        background-color: #b2e281 !important;
      }
    }
  }
  .bubble_default .ar-icon{fill: #b2e281;}
  .bubble_primary .ar-icon{fill: #b2e281;}
  .bubble_primary .mini-player__time{color: #5f9c2a}
</style>

<template>
  <div class="mini-player ">
    <div class="mini-player-actions">
      <icon-button
        id="play"
        class="mini-player__play ar-icon "
        :name="playBtnIcon"
        :class="{'mini-player__play--active': isPlaying}"
        @click.native="decorator(playback)"/>
    </div>
    <div class="mini-player-bar">
      <div class="mini-player__time" v-show="duration != playedTime">{{playedTime}}</div>
      <line-control
        class="mini-player__progress"
        ref-id="progress"
        :percentage="progress"
        @change-linehead="_onUpdateProgress"/>
      <div class="mini-player__time" v-show="duration == playedTime">{{duration}}</div>
    </div>
    <audio :id="playerUniqId" :src="audioSource"></audio>
  </div>
</template>

<script>
  import IconButton    from './icon-button'
  import LineControl   from './line-control-mini'
  import { convertTimeMMSS } from '@/lib/utils'

  export default {
    props: {
      src     : { type: String },
    },
    data () {
      return {
        isPlaying  : false,
        duration   : convertTimeMMSS(0),
        playedTime : convertTimeMMSS(0),
        progress   : 0
      }
    },
    components: {
      IconButton,
      LineControl,
    },
    mounted: function() {
      this.player = document.getElementById(this.playerUniqId)

      this.player.addEventListener('ended', () => {
        this.isPlaying = false
      })

      this.player.addEventListener('loadeddata', (ev) => {
        this._resetProgress()
        this.duration = convertTimeMMSS(this.player.duration)
      })

      this.player.addEventListener('timeupdate', this._onTimeUpdate)

      this.$eventBus.$on('remove-record', () => {
        this._resetProgress()
      })
    },
    computed: {
      audioSource () {
        let url = this.src || this.record.url
        if (url) {
          return url
        } else {
          this._resetProgress()
        }
      },
      playBtnIcon () {
        return this.isPlaying ? 'pause' : 'play'
      },
      playerUniqId () {
        return `audio-player${this._uid}`
      }
    },
    methods: {
      playback () {
        if (this.isPlaying) {
          this.player.pause()
        } else {
          console.log(this.duration)
          setTimeout(() => { this.player.play() }, 0)
        }

        this.isPlaying = !this.isPlaying
      },
      decorator (func) {
        if (!this.audioSource) {
          return
        }
        func()
      },
      _resetProgress () {
        if (this.isPlaying) {
          this.player.pause()
        }

        this.duration   = convertTimeMMSS(0)
        this.playedTime = convertTimeMMSS(0)
        this.progress   = 0
        this.isPlaying  = false
      },
      _onTimeUpdate () {
        this.playedTime = convertTimeMMSS(this.player.currentTime)
        this.progress = (this.player.currentTime / this.player.duration) * 100
        if (this.player.currentTime == this.player.duration) this.progress = 0
      },
      _onUpdateProgress (pos) {
        if (pos) {
          this.player.currentTime = pos * this.player.duration
        }
      }
    }
  }
</script>
