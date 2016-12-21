<template>

<div>
  <keep-alive>
    <video-player
            :options="videoOptions"
            ref="myPlayer"
            :config="{  youtube: true}"
            @player-state-changed="playerStateChanged"
            @onready="onReady"
            @onpause="onPause"
            @onplay="onPlay"
            @onend="onEnd"
            @onseek="onSeek"
            @resolutionchange="resolutionChange"
    >

    </video-player>
  </keep-alive>


</div>

</template>

<style scoped>
  body{
    font-family: sans-serif;
  }
  .btn{
    -webkit-user-select: none;  /* Chrome all / Safari all */
    -moz-user-select: none;     /* Firefox all */
    -ms-user-select: none;      /* IE 10+ */
    user-select: none;
    cursor: pointer;
    background: #d46161;
    color: white;
    padding: 10px;
    margin: 10px 0px;
    font-family: sans-serif;
    font-size: 14px;
    display: inline-block;
  }
  .group-input label{
    display: block;
    font-size: 13px;
    margin-top: 10px;
  }
  .group-input *{
    float: left;
    width: 100%;
  }
  input[type="text"]{
    width: 500px;
    margin: 5px 0;
    padding: 5px;
    border: 3px solid #d46161;
  }
  .note{
    font-size: 10px;
    color: #585858;
    margin: 0;
    font-family: sans-serif;
  }
</style>
<script>

require('videojs-youtube')
//resolve error: VIDEOJS: ERROR: The "Youtube" tech is undefined. Skipped browser support check for that tech.
import {videoPlayer} from './components/vue-video-player';
//overwrite package vue-video-player


export default {
  name: 'app',
  components: {
    videoPlayer
  },
  data(){

    return {
      videoOptions: {
        source:[
            {
                type: "video/youtube",
                src: "https://www.youtube.com/watch?v=iD_MyDbP_ZE"
            },
            {
                type:"video/youtube",
                src:"https://www.youtube.com/watch?v=lcHlywuHNwI"
            }

        ]
        ,
        techOrder: ["youtube"],
        autoplay: false,
        controls: true,
        ytControls: true
      },
      remainingTime:null,
      currentTime:null,
      duration:null
    }

  },
  mounted(){
    console.log(this.$refs.myPlayer.$el);

  },

  methods: {
     onPause(){
      console.log('on Pause');
    },
    onPlay(){
      console.log('on Play');
    },
    onEnd(){
      console.log('video has completed');
    },
    onReady(data){
      this.duration = data.duration;
      console.log('on Ready')
    },

    onSeek(seek){
      console.log('on Seek at:',seek.seek_at)
    },
    playerStateChanged(playerCurrentState) {
      if(typeof playerCurrentState.currentTime != 'undefined'){
        this.currentTime = playerCurrentState.currentTime;
        this.remainingTime = playerCurrentState.remainingTime;
      }
    },
    showLogCurrentTime(){
      if(!this.currentTime) return false;
      console.log('video current time has changed :',this.currentTime);
    },
    resolutionChange(){
      console.log('Resolution Change');
    }
  }
}
</script>

