<template>
  <div id="app">
       
  <div style="top:0; position: fixed; width:100%">
     <mt-header   title="文件名" >
      <router-link to="./" slot="left">
        <mt-button icon="back" >返回</mt-button>
      </router-link>
     </mt-header>
  
  </div>
    
    <div v-show="false" class="title">
        <span class="back" @click="back">&lt;</span>
        <span class="head">课程界面</span>
    </div>

    <div class="main">
      <!-- <img alt="Vue logo" src="./assets/logo.png">
      <HelloWorld msg="Welcome to Your Vue.js App"/>
      <img alt="Vue logo" src="./assets/logo.png"> -->
    </div>
    <div class="footer">
      <VueAudio :theUrl="musicItem.url" :theControlList="musicItem.controlList"/>
    </div>
  </div>
</template>
 

<script>            
import VueAudio from "@/views/ClassItem/VueAudio";
import { findCourseById } from "@/api/api";
import { mapActions, mapGetters } from "vuex";
import { MessageBox } from "mint-ui";
export default {
  components: {
    VueAudio
  },
  data() {
    return {
      course: {},
      musicItem: 
        {
          url: './static/falling-star.mp3',
          controlList: 'onlyOnePlaying'
        },
      selected: "1",
      isWantLearn: true //想学icon红心是否点亮，依赖于用户我的收藏中是否存在当前课程
    };
  },
  computed: {
    ...mapGetters({
      userData: "getUserData"
    }),
    userIsHave(){             //当前用户是否拥有此门课程的播放权限
      for(let item of this.userData.nowLearnClass){
        if(item.id == this.course.id){
          return true;
        }
      }
      return false;
    }
  },
  methods: {
    back() {
      this.$router.go("-1");
      console.log(111)
    },
    wantLearn() {            //点亮红心操作
      if (this.userData.name) {
         this.isWantLearn = !this.isWantLearn;
      }else{
        MessageBox("提示", "请先登录");
        this.$router.push('/account/login');
      }
    },
    ...mapActions(["addUserClass"]),   //通过vuex给用户添加课程
    addUserCourse(course) {           //添加课程操作
      if (this.userData.name) {
        if (!this.checkIsExist(course)) {
          //如果我的课程尚未存在
          this.addUserClass({ id: course.id, progress: 0 });
          MessageBox("提示", "添加学习成功！");
        } else {
          MessageBox("提示", "课程已经存在！");
        }
      }else{
        MessageBox("提示", "请先登录");
        this.$router.push('/account/login');
      }
    },
    checkIsExist(course) {
      for (let item of this.userData.nowLearnClass) {
        if (item.id == course.id) return true;
      }
      return false;
    }
  },
  mounted() {
    findCourseById({ id: this.$route.query.id }).then(res => {
      this.course = res;
    });
   
  }
};
</script>

<style>
:root{
  --footer-height: 50px;
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
.main{
  padding-bottom: var(--footer-height);
  overflow-y: auto;
}
.footer{
  position: fixed;
  bottom: 0;
  width: 100%;
  line-height: var(--footer-height);
  background: #42b983;
  color: #fff;
}

.title{
    position: fixed;
    top: 0;
    right: 0;
    left: 0;
    height: 1.2rem;
    background-color: white;
    line-height: 1.2rem;
    z-index: 2;
}

.back{
      font-size: 0.8rem;
      margin-left: 0.3rem;
}

.head{
      font-size: 0.6rem;
      position: absolute;
      left: 38%;
    }
</style>