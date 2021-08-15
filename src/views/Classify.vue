<template>
  <div class="wrapper">
    <!-- <search :isSearching="false" @click.native="changeToSearch" class="search"></search> -->
    <ul class="left">
      <li v-for="(item, index) in classes" :key="item.id" class="left-class" :class="item.isClick?'active':''" @click="changeTitle(index)">
        <span class="title">{{item.name}}</span>
      </li>
    </ul>
    <div class="right">
      <!-- <div v-for="src in nowClass.img" :key="src.id" class="image">
        <img :src="src" alt="">
      </div> -->
      <!-- <ul v-for="item in nowClass" :key="item.id" > -->
      <!-- <ul  >
        <span class="head">{{item.head}}</span>
        <div class="someclass">
          <li v-for="item in nowClass" :key="item.id">
          <span class="detail">{{item.title}}</span>
          </li>
        </div>
      </ul> -->
        <!-- <div id="management">
    <list :listData='listData'></list>
    </div> -->
       <!-- <typeCatalog :typeId="typeId"  v-loading="false"/> -->
  <mt-index-list >
    <!-- <mt-index-section :index="item.title" v-for="item in cityArr"> -->
    <mt-index-section >
      <mt-cell
        :title="i.title"
        v-for="i in nowClass"
        @click.native="selectedCity(i)"
      >
      </mt-cell>
    </mt-index-section>
  </mt-index-list>
    </div>
    
  </div>
</template>

<script>
import search from "@/components/search"
import typeCatalog from "@/components/typeCatalog"
import { getCourseTypes } from "@/api/api"
import { findCourseByTypeId } from "@/api/api"
export default {
  data() {
    return {
      typeId: 1, 
      classes: [],
      nowClass:{}
      // listData: [
      //   { icon: local, title: "11111", url: "/info/managerCenter"},
      //   { icon: material, title: "1", url: "/info/managerCenter"},
      //   { icon: caseHouse, title: "2", url: "/info/managerCenter"},
      //   { icon: feedBack, title: "4", url: "/info/managerCenter"}
      // ]
    };
  },
  components: {
    search,
    typeCatalog
  },
  methods: {
    selectedCity(i) {
      this.$router.push({
        
        path: "/home/coursedetails",
        query: { id: i.id }
      });
      // this.$router.push("/home/coursedetails/playing");
    },
    changeToSearch() {
      this.$router.push({ path: "home/search" });
    },
    changeTitle(key) {         //改变css样式（当前点亮）
    //  debugger
      let classes = this.classes;
      for (let item of classes) {
        item.isClick = false;
      }
      classes[key].isClick = true;
      this.nowClass = classes[key];
      let courseTypeId = classes[key].id
      findCourseByTypeId({id: courseTypeId}).then(res => {
            // console.log(res);
            // let classes = res;
            // for (let item of classes) {
            //   // this.$set(item, "isClick", false);
            //   item.isClick = false
            // }
            // classes[0].isClick = true;
            // this.classes = classes
            this.nowClass = res;
           
      });
    

    }
  },
  mounted() {
    getCourseTypes().then(res => {
      // console.log(res);
      let classes = res;

      for (let item of classes) {
        // this.$set(item, "isClick", false);
        item.isClick = false
      }
      classes[0].isClick = true;
      this.classes = classes
      // debugger
      this.nowClass = classes[0];

      this.changeTitle(0);
    });

    
  }
};
</script>

<style lang="stylus" scoped>
.wrapper
  position fixed
  height 92%
  width 100%
  overflow scroll
  .search
    position fixed
    overflow hidden
  .mint-search
    height initial
  .left
    position fixed
    width 2.56rem
    height 100%
    background-color #f7f9fc
    margin 0
    padding 0
    list-style-type none
    // margin-top 1.28rem
    .left-class
      height 1.4506rem
      font-size 0.354rem
      line-height 1.4506rem
      text-align left
      .title
        display block
        margin-left 0.42667rem
      &.active
        background-color white
        font-weight bolder
        position relative
        &:before
          content ''
          position absolute
          width 0.128rem
          height 1.4506rem
          background-color #2cc17b
  .right
    width 6.61323rem
    // margin 1rem
    margin-left 2.9867rem
    // margin-top 1.28rem
    margin-right 0.42666rem
    margin-bottom 0.85332rem
    .image
      display inline-block
      width 6.61323rem
      &> img
        width 6.61323rem
        height 2.56rem
    .right-class
      list-style-type none
      margin 0
      padding 0
      margin-top 0.42666rem
      .head
        font-weight bolder
        display block
        position relative
        &:after
          border solid 1px black
          border-bottom-width 0
          border-left-width 0
          content ' '
          top 55%
          position absolute
          width 7px
          height 7px
          -webkit-transform translateY(-50%) rotate(45deg)
          transform translateY(-50%) rotate(45deg)
      .someclass
        &>li
          flex 1
          display inline-block
          margin-top 0.42666rem
          margin-right 0.085332rem
        .detail
          width 2.048rem
          height 0.7111rem
          line-height 0.7111rem
          // margin
          display block
          border 1px solid #d5dae7
          border-radius 0.42667rem
          text-align center
          font-size 0.34134rem
          &:active
            background-color #f2f4f7
</style>

