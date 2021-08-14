<template>
  <div class="di main-wrap" v-loading="audio.waiting">
    <!-- 这里设置了ref属性后，在vue组件中，就可以用this.$refs.audio来访问该dom元素 引起 底部高度非常大 -->
  <div>  
  <!-- <div > -->
    <!-- 这里设置了ref属性后，在vue组件中，就可以用this.$refs.audio来访问该dom元素 -->
    <audio ref="audio" class="dn" 
    :src="url" 
    @play="onPlay" 
    @error="onError"
    @waiting="onWaiting"
    @pause="onPause" 
    @timeupdate="onTimeupdate" 
    @loadedmetadata="onLoadedmetadata"
    
    ></audio>

    <div>
      <mt-button  @click="startPlayOrPause">{{audio.playing | transPlayPause}}</mt-button>
    </div>     
      <!-- <el-button v-show="!controlList.noSpeed" type="text" @click="changeSpeed">{{audio.speed | transSpeed}}</el-button> -->
      
      <div style="width:95%;  margin:0 auto;  background:red">
      <mt-range  v-model="sliderTime" :format-tooltip="formatProcessToolTip" @change="changeCurrentTime" @input="inputEvent" ></mt-range>
      </div>

      <div style="width:98%;  margin:0 auto;  background:red">
        <div style="text-align:left;float:left "><span  style="text-align:left; width:500px; background:blue" type="info">{{ audio.currentTime | formatSecond}}</span></div>
        <div style="text-align:right;float:right"><span  style="text-align:right;width:50%; background:blue" type="info">{{ audio.maxTime | formatSecond }}</span></div>
        
      </div>
      
      <!-- <mt-button v-show="!controlList.noMuted"  @click="startMutedOrNot">{{audio.muted | transMutedOrNot}}</mt-button> -->

      <!-- <el-slider v-show="!controlList.noVolume" v-model="volume" :format-tooltip="formatVolumeToolTip" @change="changeVolume" class="slider"></el-slider> --> 
      

  </div>
</template>

<script>
import { findCourseFileById } from "@/api/api";
  function realFormatSecond(second) {
    var secondType = typeof second

    if (secondType === 'number' || secondType === 'string') {
      second = parseInt(second)

      var hours = Math.floor(second / 3600)
      second = second - hours * 3600
      var mimute = Math.floor(second / 60)
      second = second - mimute * 60

      return hours + ':' + ('0' + mimute).slice(-2) + ':' + ('0' + second).slice(-2)
    } else {
      return '0:00:00'
    }
  }

  export default {
    props: {
      theLoading: {
        type: Boolean,
        required: false,
      },
      theUrl: {
        type: String,
        required: true,
      },
      theSpeeds: {
        type: Array,
        default () {
          return [1, 1.5, 2]
        }
      },
      theControlList: {
        type: String,
        default: ''
      }
    },
    name: 'VueAudio',
    data() {
      console.log(" 子组件data url： ", this.theUrl);
      return {
        waiting: true,
        // loadingA: this.theLoading,
        url: this.theUrl || 'http://devtest.qiniudn.com/secret base~.mp3',
        audio: {
          currentTime: 0,
          maxTime: 0,
          playing: false,
          muted: false,
          speed: 1,
          waiting: true,
          preload: 'auto'
        },

        sliderTime: 0,
        volume: 100,
        speeds: this.theSpeeds,

        controlList: {
          // 不显示下载
          noDownload: false,
          // 不显示静音
          noMuted: false,
          // 不显示音量条
          noVolume: false,
          // 不显示进度条
          noProcess: false,
          // 只能播放一个
          onlyOnePlaying: false,
          // 不要快进按钮
          noSpeed: false
        }
      }
    },
    mounted() {
    // debugger
    // this.$route.query.id = 1
    // console.log("子 mount")
    // this.loadingA  = true;
    // findCourseFileById({ id: 1 }).then(res => {
    //   // debugger
    //   // this.courseFile = res;
    //   this.url  = res.mp3_src;
    //   // this.$refs.audio.urlA = res.mp3_src;
    //   this.loadingA = false;

    //   console.log("findCourseFileById 完成")
    // }); 
    
    
    // this.$refs.audio.preload  = true
    // document.body.addEventListener("touchmove", that.defferScroll, {passive: false});
    // document.body.addEventListener("wheel", that.defferScroll, {passive: false});
  },
    methods: {
      setControlList () {
        let controlList = this.theControlList.split(' ')
        controlList.forEach((item) => {
          if(this.controlList[item] !== undefined){
            this.controlList[item] = true
          }
        })
      },
      changeSpeed() {
        let index = this.speeds.indexOf(this.audio.speed) + 1
        this.audio.speed = this.speeds[index % this.speeds.length]
        this.$refs.audio.playbackRate = this.audio.speed
      },
      startMutedOrNot() {
        this.$refs.audio.muted = !this.$refs.audio.muted
        this.audio.muted = this.$refs.audio.muted
      },
      // 音量条toolTip
      formatVolumeToolTip(index) {
        return '音量条: ' + index
      },
      // 进度条toolTip
      formatProcessToolTip(index = 0) {
        index = parseInt(this.audio.maxTime / 100 * index)
        return '进度条: ' + realFormatSecond(index)
      },
      // 音量改变
      changeVolume(index = 0) {
        this.$refs.audio.volume = index / 100
        this.volume = index
      },
      // 播放跳转
      changeCurrentTime(index) {
        this.startPlay()
        // this.$refs.audio.currentTime = parseInt(index / 100 * this.audio.maxTime)
      },

      inputEvent(index) {
        // debugger
        this.pausePlay() 
        this.$refs.audio.currentTime = parseInt(index / 100 * this.audio.maxTime)
      },
      
      startPlayOrPause() {
        return this.audio.playing ? this.pausePlay() : this.startPlay()
      },
      // 开始播放
      startPlay() {
        this.$refs.audio.play()
      },
      // 暂停
      pausePlay() {
        this.$refs.audio.pause()
      },
      // 当音频暂停
      onPause () {
        this.audio.playing = false
      },
      // 当发生错误, 就出现loading状态
      onError () {
        this.audio.waiting = true
      },
      // 当音频开始等待
      onWaiting (res) {
        console.log(res)
      },
      // 当音频开始播放
      onPlay (res) {
        console.log(res)
        this.audio.playing = true
        this.audio.loading = false

        if(!this.controlList.onlyOnePlaying){
          return 
        }

        let target = res.target

        let audios = document.getElementsByTagName('audio');

        [...audios].forEach((item) => {
          if(item !== target){
            item.pause()
          }
        })
      },
      // 当timeupdate事件大概每秒一次，用来更新音频流的当前播放时间
      onTimeupdate(res) {
        // console.log('timeupdate')
        // console.log(res)
        this.audio.currentTime = res.target.currentTime
        this.sliderTime = parseInt(this.audio.currentTime / this.audio.maxTime * 100)
      },
      // 当加载语音流元数据完成后，会触发该事件的回调函数
      // 语音元数据主要是语音的长度之类的数据
      onLoadedmetadata(res) {
        console.log('loadedmetadata')
        console.log(res)
        this.audio.waiting = false
        this.audio.maxTime = parseInt(res.target.duration)
      }
    },
    filters: {
      formatSecond(second = 0) {
        return realFormatSecond(second)
      },
      transPlayPause(value) {
        return value ? '暂停' : '播放'
      },
      transMutedOrNot(value) {
        return value ? '放音' : '静音'
      },
      transSpeed(value) {
        return '快进: x' + value
      }
    },
    created() {
      this.setControlList()
    }
  }

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .main-wrap{
    padding: 10px 15px;
  }

  .di {
    display: inline-block;
  }

  .download {
    color: #409EFF;
    margin-left: 15px;
  }

  .dn{
    display: none;
  }
</style>
