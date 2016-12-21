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
  <div  v-if="currentTime">
    <h5>video : {{video_title}}</h5>
    <div>Current time has changed : {{currentTime}}</div>
    <div>Current Time video: {{convertSecond2Time(currentTime)}}</div>
    <div>Available Time video: {{convertSecond2Time(remainingTime)}}</div>
  </div>

</div>

</template>

<style scoped>
  body{
    font-family: sans-serif;
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
import axios from 'axios';
import _ from 'lodash';

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
                src: "https://www.youtube.com/watch?v=Vpg9yizPP_g"
            },
            {
                type:"video/youtube",
                src:"https://www.youtube.com/watch?v=3Nu74DGCjV8"
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
      duration:null,
      currentSrc:null,
      video_title:null
    }
  },


  created(){
      var _this = this;
      _.forEach(
          this.videoOptions.source,
          function(source,i){
            axios.get('https://noembed.com/embed',{
              params: {
                format: 'json',
                url: source.src
              }
            })
            .then(function (response) {
              _this.videoOptions.source[i].title = response.data.title;
            })
            .catch(function (error) {
              console.log(error);
            });
          }

      )

  },
  mounted(){

  },

  watch:{
    currentSrc(newVal,oldVal){
      this.video_title = this.videoOptions.source.filter(function (v, i) {
        return v.src == newVal;
      })[0].title;
    }
  },


  methods: {
     onPause(){
      console.log('on Pause');
    },
    onPlay(data){
      this.currentSrc = data.src;
      console.log('on Play');
    },
    onEnd(){
      console.log('video has completed');
    },
    onReady(data){
      this.currentSrc = data.src;
      this.duration = data.duration;
      console.log('on Ready')
    },

    onSeek(seek){
      console.log('on Seek at:',seek.seek_at , '('+ this.convertSecond2Time(seek.seek_at) + ')')
    },
    playerStateChanged(playerCurrentState) {
      if(typeof playerCurrentState.currentTime != 'undefined'){
        this.currentTime = playerCurrentState.currentTime;
        this.remainingTime = playerCurrentState.remainingTime;
      }
    },

    convertSecond2Time(s){
      var date = new Date(1970,0,1);
      date.setSeconds(s);
      return date.toTimeString().replace(/.*(\d{2}:\d{2}:\d{2}).*/, "$1");
    },
    resolutionChange(){
      console.log('Resolution Change');
    },
    getIdYoutube(url){

        var regExp = /^.*((youtu.be\/)|(v\/)|(\/u\/\w\/)|(embed\/)|(watch\?))\??v?=?([^#\&\?]*).*/;
        var match = url.match(regExp);
        return (match&&match[7].length==11)? match[7] : false;

    }
  }
}
</script>

