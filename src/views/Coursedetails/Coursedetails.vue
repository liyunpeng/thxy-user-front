<template>
  <div class="wrapper">
    <!-- <div style="top:0; position: fixed; width:100%"> -->
    <mt-header :title="title" fixed>
      <router-link slot="left" to="">
        <mt-button icon="back" @click="backA">返回</mt-button>
      </router-link>
    </mt-header>
    <!-- </div>  -->
  <div>
    防止catalog位置太靠上
  </div>

    <catalog :theCourseFiles="courseFiles" />

    <!-- <img v-show="false" :src="course.imgSrc" alt="" class="main-image" v-if="!userData.name || !userIsHave">
    <div>
    <mt-navbar v-show="false"   v-model="selected">
        <mt-tab-item id="1">目录</mt-tab-item>
        <mt-tab-item id="2">介绍</mt-tab-item>
        <mt-tab-item id="3" v-show="false">评价</mt-tab-item>
    </mt-navbar>
    <mt-tab-container v-show="false"  v-model="selected" swipeable>
       <mt-tab-container-item id="1">
        <catalog :course="course"/>
      </mt-tab-container-item>
      <mt-tab-container-item id="2">
        <introduce :course="course"/>
      </mt-tab-container-item>
      <mt-tab-container-item id="3">
        <comment :course="course"/>
      </mt-tab-container-item>
    </mt-tab-container>
    </div>
    
    <div class="fixed" v-show="false">
        <div class="want" @click="wantLearn">
            <i class="icon iconfont icon-xiangqu" :class="isWantLearn? 'active':''"></i>
            <span>想学</span>
        </div>
        <div class="addtostudy" @click="addUserCourse(course)">
            <i class="icon iconfont icon-jia"></i>
            <span>加入学习</span>
        </div>
    </div> -->
  </div>
</template>

<script>
import catalog from "@/views/Coursedetails/catalog";
// import comment from "@/views/Coursedetails/comment";
// import introduce from "@/views/Coursedetails/introduce";
import { findCourseFileByCourseId } from "@/api/api";
import { mapActions, mapGetters } from "vuex";
// import { MessageBox } from "mint-ui";
export default {
  components: {
    catalog,
    // comment,
    // introduce
  },
  data() {
    return {
      courseFiles: [],
      courseId: this.$route.query.id,
      course: {},
      title: this.$route.query.title,
      audios: [
        {
          url: "./static/falling-star.mp3",
          controlList: "onlyOnePlaying",
        },
      ],
      selected: "1",
      isWantLearn: true, //想学icon红心是否点亮，依赖于用户我的收藏中是否存在当前课程
    };
  },
  computed: {
    ...mapGetters({
      userData: "getUserData",
    }),
    userIsHave() {
      //当前用户是否拥有此门课程的播放权限
      for (let item of this.userData.nowLearnClass) {
        if (item.id == this.course.id) {
          return true;
        }
      }
      return false;
    },
  },
  methods: {
    backA() {
      this.$router.go("-1");
      // classify
      //  this.$router.push('/classify');
      // console.log(111)
    },
    // wantLearn() {            //点亮红心操作
    //   if (this.userData.name) {
    //      this.isWantLearn = !this.isWantLearn;
    //   }else{
    //     MessageBox("提示", "请先登录");
    //     this.$router.push('/account/login');
    //   }
    // },
    // ...mapActions(["addUserClass"]),   //通过vuex给用户添加课程
    // addUserCourse(course) {           //添加课程操作
    //   if (this.userData.name) {
    //     if (!this.checkIsExist(course)) {
    //       //如果我的课程尚未存在
    //       this.addUserClass({ id: course.id, progress: 0 });
    //       MessageBox("提示", "添加学习成功！");
    //     } else {
    //       MessageBox("提示", "课程已经存在！");
    //     }
    //   }else{
    //     MessageBox("提示", "请先登录");
    //     this.$router.push('/account/login');
    //   }
    // },
    checkIsExist(course) {
      for (let item of this.userData.nowLearnClass) {
        if (item.id == course.id) return true;
      }
      return false;
    },
  },

  beforeRouteEnter(to, from, next) {
    // debugger;
    var a = from.path.length;
    var b = "/home/coursedetails/playing".length;
    console.log(" ab: ", a != b);
    // 遇到浏览器不能正确判断 a!=b
    var cc = a != b;
    if (cc) {
      to.meta.isBack = false;
      console.log(" to.meta.isBack = false ");
    } else {
      to.meta.isBack = true;
      console.log(" to.meta.isBack = true");
    }
    next();
  },

  activated() {
    this.title = this.$route.query.title;
    if (!this.$route.meta.isBack) {
      console.log(" activated执行 不使用缓存， 走网络刷新");
      findCourseFileByCourseId({ id: this.$route.query.id }).then((res) => {
        this.courseFiles = res;
      });
    }else{
       console.log(" activated执行 使用缓存， 不走网络");
    }
  },

  mounted() {
    console.log("分类页面mounted执行");
    this.title = this.$route.query.title;
    findCourseFileByCourseId({ id: this.$route.query.id }).then((res) => {
      this.courseFiles = res;

      // courseFiles: {},
    });
  },
};
</script>

<style lang="stylus" scoped>
.wrapper {
  background-color: white;

  .title {
    position: fixed;
    top: 0;
    right: 0;
    left: 0;
    height: 1.2rem;
    background-color: white;
    line-height: 1.2rem;
    z-index: 2;

    .back {
      font-size: 0.8rem;
      margin-left: 0.3rem;
    }

    .head {
      font-size: 0.6rem;
      position: absolute;
      left: 38%;
    }
  }

  video {
    margin-top: 1.2rem;
    width: 10rem;
    height: 5.8rem;
  }

  .main-image {
    margin-top: 1.2rem;
    width: 10rem;
    height: 5.5rem;
  }

  .fixed {
    position: fixed;
    bottom: 0;
    left: 0;
    right: 0;
    height: 1rem;
    font-size: 0;
    text-align: center;
    line-height: 1rem;
    border-top: 1px solid #e6eaf2;
    margin-bottom: -1px;

    .want {
      width: 5rem;
      display: inline-block;
      background-color: white;
      font-size: 0.4rem;

      .active {
        color: red;
      }
    }

    .addtostudy {
      width: 5rem;
      display: inline-block;
      background-color: #ff632a;
      font-size: 0.4rem;
    }
  }
}
</style>
