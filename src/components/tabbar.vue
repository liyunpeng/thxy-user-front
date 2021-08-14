<template>
  <div class="tab">
    <mt-tabbar  v-show="isShowTabbar" fixed>
      <mt-tab-item id="首页" class="tabbar-item" @click.native="changeToMain">
        <i slot="icon" class="icon iconfont icon-shouye" :style="maincolor"></i>
        首页
      </mt-tab-item>
      <mt-tab-item id="分类" class="tabbar-item" @click.native="changeToClass">
        <i slot="icon" class="icon iconfont icon-leimupinleifenleileibie2" :style="classifycolor"></i>
        分类
      </mt-tab-item>
      <mt-tab-item id="我的学习" class="tabbar-item" @click.native="changeToLearn">
        <i slot="icon" class="icon iconfont icon-icon" :style="learncolor"></i>
        我的学习
      </mt-tab-item>
      <mt-tab-item id="账号" class="tabbar-item" @click.native="changeToMy">
        <i slot="icon" class="icon iconfont icon-icon_account" :style="mycolor"></i>
        账号
      </mt-tab-item>
    </mt-tabbar>
  </div>
  
</template>

<script>
const iconColor = "color: #cdcdcd";
const iconColorActive ="color: #3c4a55";
export default {  
    data(){
      return{
        maincolor: iconColorActive,
        classifycolor: iconColor,
        learncolor: iconColor,
        mycolor: iconColor,
      }
    },
   	computed: {
      isShowTabbar () {
        // debugger
        let aac = this.$route.path.split('/');
        console.log(aac);
      
        let routeLength = aac.length

        if (routeLength  == 3 ) {
          if (aac[2] =='') {
              return true;
          }
         }else{
            return routeLength <= 2 ? true : false;
         }
      }
 
    }, 
    methods:{
      changeToMain(){
        this.$router.push({path:'/home'});
        this.maincolor = iconColorActive;
        this.classifycolor = iconColor;
        this.learncolor = iconColor;
        this.mycolor = iconColor;
      },
      changeToClass(){
        this.$router.push({path:'/classify'});
        this.maincolor = iconColor;
        this.classifycolor = iconColorActive;
        this.learncolor = iconColor;
        this.mycolor = iconColor;        
      },
      changeToLearn(){
        this.$router.push({path:'/mystudy'});       
        this.maincolor = iconColor;
        this.classifycolor = iconColor;
        this.learncolor = iconColorActive;
        this.mycolor = iconColor;   
      },
      changeToMy(){
        this.$router.push({path:'/account'});      
        this.maincolor = iconColor;
        this.classifycolor = iconColor;
        this.learncolor = iconColor;
        this.mycolor = iconColorActive;    
      },
    }
}
</script>

<style lang="stylus" scoped>
.tab
  .tabbar-item
    display: inline-block
    .icon 
      font-size: 25px
  .mint-tabbar 
    height: 8%
  .mint-tab-item
    vertical-align center
    display: flex
    flex-direction: column
    align-items: center
    justify-content: center
</style>

