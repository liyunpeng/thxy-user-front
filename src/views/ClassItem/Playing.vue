<template>
  <div id="app">
    <div style="top: 0; position: fixed; width: 100%">
      <mt-header :title="title">
        <router-link to="" slot="left">
          <mt-button icon="back" @click="backA">返回</mt-button>
        </router-link>
      </mt-header>
    </div>

    <!-- <div v-show="false" class="title">
        <span class="back" @click="back">&lt;</span>
        <span class="head">课程界面</span>
    </div> -->

    <div class="main">
      <img src="../assets/logo.png" />
      <!-- <HelloWorld msg="Welcome to Your Vue.js App"/>
      <img alt="Vue logo" src="./assets/logo.png"> -->
    </div>

    <div class="footer" v-loading="loadingA">
      <audio
        ref="audio"
        class="dn"
        :src="mp3Src"
        @play="onPlay"
        @error="onError"
        @waiting="onWaiting"
        @pause="onPause"
        @timeupdate="onTimeupdate"
        @loadedmetadata="onLoadedmetadata"
      ></audio>

      <!-- <el-button v-show="!controlList.noSpeed" type="text" @click="changeSpeed">{{audio.speed | transSpeed}}</el-button> -->

      <div style="width: 95%; margin: 0 auto;  background: red">
        <mt-range
          v-model="sliderTime"
          :format-tooltip="formatProcessToolTip"
          @change="changeCurrentTime"
          @input="inputEvent"
        ></mt-range>
      </div>

      <div style="width: 95%; margin: 0 auto; background: red; line-height:20px">
        <div style="text-align: left; float: left; height:0px">
          <span
            style="text-align: left; background: blue"
            type="info"
            >{{ audio.currentTime | formatSecond }}</span
          >
        </div>
        <div style="text-align: right; float: right">
          <span
            style="text-align: right;  background: blue"
            type="info"
            >{{ audio.maxTime | formatSecond }}</span
          >
        </div>
      </div>

      <!-- <div style="float: clear； display:block"  >
        </div> -->
      <div style="  margin: 10px;" >
        <mt-button @click="startPlayOrPause">{{
          audio.playing | transPlayPause
        }}</mt-button>
      </div>

      <!-- <mt-button v-show="!controlList.noMuted"  @click="startMutedOrNot">{{audio.muted | transMutedOrNot}}</mt-button> -->

      <!-- <el-slider v-show="!controlList.noVolume" v-model="volume" :format-tooltip="formatVolumeToolTip" @change="changeVolume" class="slider"></el-slider> -->
    </div>
  </div>
</template>
 

<script>
import { findCourseFileById } from "@/api/api";
import { mapActions, mapGetters } from "vuex";
import { MessageBox } from "mint-ui";
function realFormatSecond(second) {
  var secondType = typeof second;

  if (secondType === "number" || secondType === "string") {
    second = parseInt(second);

    var hours = Math.floor(second / 3600);
    second = second - hours * 3600;
    var mimute = Math.floor(second / 60);
    second = second - mimute * 60;

    return (
      hours + ":" + ("0" + mimute).slice(-2) + ":" + ("0" + second).slice(-2)
    );
  } else {
    return "0:00:00";
  }
}
export default {
  components: {
    // VueAudio
  },
  data() {
    let urlLocal = "";
    // // this.$route.query.id = 1
    // await findCourseFileById({ id: 1 }).then(res => {
    //   // debugger
    //   // this.courseFile = res;
    //   urlLocal  = res.mp3_src;
    //   console.log("url123=", urlLocal)
    // });
    // let res = await findCourseFileById({ id: 1 });
    // urlLocal  = res.mp3_src;
    // console.log("url=", urlLocal)
    return {
      loadingA: false,
      title : '',
      defferScroll: function (event) {
        event.preventDefault();
      },
      mp3Src: "",
      courseFile: {},
      musicItem: {
        // url: './static/falling-star.mp3',
        // url: 'http://localhost:8082/api/fileDownload',
        // url: this.courseFile.mp3_src,
        url: "",
        controlList: "onlyOnePlaying",
      },
      selected: "1",
      isWantLearn: true, //想学icon红心是否点亮，依赖于用户我的收藏中是否存在当前课程

      // waiting: true,
      // loadingA: this.theLoading,
      // url: this.theUrl || 'http://devtest.qiniudn.com/secret base~.mp3',
      audio: {
        currentTime: 0,
        maxTime: 0,
        playing: false,
        muted: false,
        speed: 1,
        waiting: true,
        preload: "auto",
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
        noSpeed: false,
      },
    };
  },
  computed: {
    ...mapGetters({
      userData: "getUserData",
    }),
    userIsHave() {
      //当前用户是否拥有此门课程的播放权限
      for (let item of this.userData.nowLearnClass) {
        if (item.id == this.courseFile.id) {
          return true;
        }
      }
      return false;
    },
  },
  filters: {
    formatSecond(second = 0) {
      return realFormatSecond(second);
    },
    transPlayPause(value) {
      return value ? "暂停" : "播放";
    },
    transMutedOrNot(value) {
      return value ? "放音" : "静音";
    },
    transSpeed(value) {
      return "快进: x" + value;
    },
  },
  created() {
    // this.setControlList();
  },
  methods: {
    backA() {
      this.$router.go("-1");
    },
    changeSpeed() {
      let index = this.speeds.indexOf(this.audio.speed) + 1;
      this.audio.speed = this.speeds[index % this.speeds.length];
      this.$refs.audio.playbackRate = this.audio.speed;
    },
    startMutedOrNot() {
      this.$refs.audio.muted = !this.$refs.audio.muted;
      this.audio.muted = this.$refs.audio.muted;
    },
    // 音量条toolTip
    formatVolumeToolTip(index) {
      return "音量条: " + index;
    },
    // 进度条toolTip
    formatProcessToolTip(index = 0) {
      index = parseInt((this.audio.maxTime / 100) * index);
      return "进度条: " + realFormatSecond(index);
    },
    // 音量改变
    changeVolume(index = 0) {
      this.$refs.audio.volume = index / 100;
      this.volume = index;
    },
    // 播放跳转
    changeCurrentTime(index) {
      this.startPlay();
      // this.$refs.audio.currentTime = parseInt(index / 100 * this.audio.maxTime)
    },

    inputEvent(index) {
      // debugger
      this.pausePlay();
      this.$refs.audio.currentTime = parseInt(
        (index / 100) * this.audio.maxTime
      );
    },

    startPlayOrPause() {
      return this.audio.playing ? this.pausePlay() : this.startPlay();
    },
    // 开始播放
    startPlay() {
      this.$refs.audio.play();
    },
    // 暂停
    pausePlay() {
      this.$refs.audio.pause();
    },
    // 当音频暂停
    onPause() {
      this.audio.playing = false;
    },
    // 当发生错误, 就出现loading状态
    onError() {
      this.audio.waiting = true;
    },
    // 当音频开始等待
    onWaiting(res) {
      console.log(res);
    },
    // 当音频开始播放
    onPlay(res) {
      console.log(res);
      this.audio.playing = true;
      this.audio.loading = false;

      if (!this.controlList.onlyOnePlaying) {
        return;
      }

      let target = res.target;

      let audios = document.getElementsByTagName("audio");

      [...audios].forEach((item) => {
        if (item !== target) {
          item.pause();
        }
      });
    },
    // 当timeupdate事件大概每秒一次，用来更新音频流的当前播放时间
    onTimeupdate(res) {
      // console.log('timeupdate')
      // console.log(res)
      this.audio.currentTime = res.target.currentTime;
      this.sliderTime = parseInt(
        (this.audio.currentTime / this.audio.maxTime) * 100
      );
    },
    // 当加载语音流元数据完成后，会触发该事件的回调函数
    // 语音元数据主要是语音的长度之类的数据
    onLoadedmetadata(res) {
      console.log("loadedmetadata");
      console.log(res);
      this.audio.waiting = false;
      this.audio.maxTime = parseInt(res.target.duration);
    },
    back() {
      this.$router.go("-1");
      console.log(111);
    },
    wantLearn() {
      //点亮红心操作
      if (this.userData.name) {
        this.isWantLearn = !this.isWantLearn;
      } else {
        MessageBox("提示", "请先登录");
        this.$router.push("/account/login");
      }
    },
    ...mapActions(["addUserClass"]), //通过vuex给用户添加课程
    addUserCourse(course) {
      //添加课程操作
      if (this.userData.name) {
        if (!this.checkIsExist(course)) {
          //如果我的课程尚未存在
          this.addUserClass({ id: course.id, progress: 0 });
          MessageBox("提示", "添加学习成功！");
        } else {
          MessageBox("提示", "课程已经存在！");
        }
      } else {
        MessageBox("提示", "请先登录");
        this.$router.push("/account/login");
      }
    },
    checkIsExist(course) {
      for (let item of this.userData.nowLearnClass) {
        if (item.id == course.id) return true;
      }
      return false;
    },
  },
  mounted() {
    // debugger
    // this.$route.query.id = 1
    console.log("父 mount");
    this.loadingA = true;

    findCourseFileById({ id: this.$route.query.id }).then((res) => {
      // debugger
      this.mp3Src = res.mp3_url + "?fileName=" + res.mp3_file_name;
      this.title = res.mp3_file_name.split('.')[0]
      console.log("mp3 src: ", this.mp3Src);

      console.log("findCourseFileById 完成");
      this.loadingA = false;
    });
  },
};
</script>

<style>
:root {
  --footer-height: 30px;
}
body {
  padding: 0;
  margin: 0;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.main {
  padding-bottom: var(--footer-height);
  /* overflow-y: auto; */
}
.footer {
  position: fixed;
  bottom: 0px;
  width: 100%;
  line-height: var(--footer-height);
  background: #c5d1cb;
  color: rgb(190, 207, 95);
}

.title {
  position: fixed;
  top: 0;
  right: 0;
  left: 0;
  height: 1.2rem;
  background-color: white;
  line-height: 1.2rem;
  z-index: 2;
}

.back {
  font-size: 0.8rem;
  margin-left: 0.3rem;
}

.head {
  font-size: 0.6rem;
  position: absolute;
  left: 38%;
}
</style>