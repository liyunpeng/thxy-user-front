<template>
  <mt-index-list>
    <mt-index-section :index="item.title" v-for="item in cityArr">
      <mt-cell :title="i" v-for="i in item.lists" @click.native="selectedCity(i)">
      </mt-cell>
    </mt-index-section>
  </mt-index-list>
</template>

<script>
import {mapGetters} from 'vuex'
import { getAllclass } from "@/api/api";
export default {
  data(){
    return{
      classList : [],
      allClass : [],
      cityArr:[    
     {
         "title":"A",
         "lists":[
            "阿坝","阿拉善","阿里","安康","安庆","鞍山","安顺","安阳","澳门"
            ]
     },
     {
         "title":"B",
         "lists":["北京","白银","保定","宝鸡","保山","包头","巴中","北海","蚌埠","本溪","毕节","滨州","百色","亳州"
            ]
     },
      ]
    }
  },
  computed:{
    ...mapGetters({
        user: 'getUserData'
    }),
    // classList(){
      
    // }
  },
  mounted(){
    getAllclass().then((res)=>{
      this.allClass = res;
      for(let index in this.user.nowLearnClass){    //获取到allclass信息后 查询当前用户现学课程
      this.checkList(this.user.nowLearnClass[index]);
      }
    });
    
  },
  methods:{
    selectedCity(i){
      this.$router.push('/home/coursedetails/classItem');
    },
    checkList(item){    //通过user中的所学课程id从所有课程中寻找详细课程信息
      for(let key of this.allClass){
        if(key.id == item.id){
          key.progress = item.progress;
          this.classList.push(key);
        }
      }
    },
    changeToDetail(classes){
      this.$router.push({path:"/home/coursedetails" , query:{id:classes.id}})     //通过vue-router传参跳转
    },
    changeToLogin(){
      this.$router.push('/account/login');
    }
  }
}
</script>

<style lang="stylus" scoped>
.wrapper 
  background-color: #f2f4f7;
  width: 100%;
  height: 92%;
  position: fixed;
  overflow: scroll;
  .user
    // height 7rem
    background-color: white;
    padding: .42667rem;
    .nav 
      margin-top: 8px;
      margin-bottom: 0;
    .avatar 
      width: 1.70667rem;
      height: 1.70667rem;
      border-radius: 1.70667rem;
      border: 0.14222rem solid #ced1da;
      display: inline-block;
      margin-left: .21333rem;
      margin-top: 0.21333rem;
    .description 
      display: inline-block;
      vertical-align: top;
      margin-left: 0.21333rem;
      .title 
        font-size: .64rem;
        font-weight: 600;
        margin-top: .42667rem;
        margin-bottom: 0;
      .target 
        display: inline-block;
        margin-top: 0.21333rem;
        font-size: 0.34133rem;
  .login
    height 4rem
    width 10rem
    background-color white
    text-align center
    display: flex;
    align-items: center;
    justify-content: center;
    .wrap
      width 2.2rem
      height 2.2rem
      border-radius 50%
      border 1px solid #2cc17b
      .button
        width 2rem
        height 2rem
        border-radius 50%
        background-color #2cc17b
        line-height 2rem
        margin 0.1rem
        color white
        font-size 0.5rem
        &:active
          background-color #f2f4f7
  .manage 
    background-color white
    display: flex;
    // border-bottom: .042667rem solid #ced1da;
    height 0.9rem
    line-height 0.5rem
    // height 1rem
    &>span 
      margin-top: 0.21333rem;
      flex: 1;
      text-align: center;
      border-right: 1px solid #ced1da
      margin-bottom 0.1rem
      &:active
        background-color #f2f4f7
      &:last-child 
        border-right: 0;
  .nowlearn 
    margin-top: .21333rem;
    padding: .41333rem;
    min-height: 100%;
    background-color: white;
   
    .head 
      .title 
        // display inline-block
        font-weight: 500
        font-size 0.512rem;
      .seeall 
        position: relative;
        float: right;
        font-size: 0.37133rem;
        margin-right: 0.56889rem;
        margin-top: 0.14222rem;
        color #c8c8cd;
        &:active
          background-color #f2f4f7
        &:after 
          border: solid 2px #c8c8cd;
          border-bottom-width: 0;
          border-left-width: 0;
          content: ' ';
          top: 55%;
          right: -0.42667rem;
          position: absolute;
          width: 7px;
          height: 7px;
          -webkit-transform: translateY(-50%) rotate(45deg);
          transform: translateY(-50%) rotate(45deg);
    .allclass
      list-style-type none 
      margin 0
      margin-top .42667rem
      padding 0
      .class
        display block 
        height 2.84444rem
        border-bottom 1px solid #c8c8cd
        &:active
          background-color #f2f4f7
        .class-description
          width 5.97332rem
          display inline-block
          .title
            display block 
            height 0.8532rem
            margin-top 0.21333rem
            font-size 0.512rem;
          .progress
            display block 
            margin-top  0.56887rem
            font-size 0.36974rem;
            color #29b774
        .class-img
          float right 
          display inline-block
          width 2.98662rem
          height 2rem
          margin-top .22667rem
          // margin-right .66667rem
</style>
