<style lang="scss">
  body{background: #eee}
  .toggle {
    cursor: pointer;
    margin: 20px;
  }
  .bubble_default{background:#fff;width: 280px;}
  .bubble_primary{background:#b2e281;width:280px;}
</style>

<template>
  <div class="row">
    <div class="toggle" @click="toggle">TOGGLE</div>

    <audio-recorder v-if="showRecorder"
      upload-url="some url"
      :attempts="3"
      :time="2"
      :headers="headers"
      :start-record="callback"
      :stop-record="callback"
      :start-upload="callback"
      :successful-upload="callback"
      :failed-upload="callback"/>

    <audio-player :src="mp3" v-if="!showRecorder"/>
    <br>
    <br>
    <div class="bubble_default">
      <mini-player :src="mp3"/>
    </div>
    <br>
    <div class="bubble_primary">
      <mini-player :src="mp3"/>
    </div>

  </div>
</template>

<script>
  export default {
    name: 'app',
    data () {
      return {
        mp3: 'http:\/\/cloudtest.ksapi.com\/api\/log\/file?filekey=655ba89dc91148b46eec2802d801dd7e&plus=mp3',
        showRecorder: true,
        headers: {
          'X-Custom-Header': 'some data'
        }
      }
    },
    methods: {
      callback (msg) {
        console.debug('Event: ', msg)
      },
      toggle () {
        this.showRecorder = !this.showRecorder
      }
    }
  }
</script>
