<template>
  <div id="operating" ref='operatingRef' @touchstart='touchStartFn' @touchend='touchEndFn'>
    <keep-alive>
    <router-view  class="appView"></router-view>
  </keep-alive>
  <!-- <div class="returnHomePage" @click="returnHomePageFn" id="returnHomePageId" ref="returnHomePageRef" v-show="operatingReturnHomePage">
    <img src="../../assets/image/returnHome.png" alt />
    <span>首页</span>
  </div> -->
  <!-- <div class="returnTop" @click="returnTopFn" ref="returnTopRef" v-show="operatingReturnTopPage">
    <img src="../../assets/image/returnTop.png" alt />
    <span>顶部</span>
  </div> -->
  <van-tabbar v-model="active" route :style="{'padding-bottom':$store.state.paddingBottom}" v-if="bottomShow">
  	<van-tabbar-item replace :to="{path : '/operating/operating_index',query:{transition:'def',time: new Date().getTime().toString()}}">
  	    <span>医院</span>
  	    <img
  			slot="icon"
  			slot-scope="props"
  			:src="props.active ? index.inactive : index.active"
  	    />
  	</van-tabbar-item>
  	<van-tabbar-item replace :to="{path:'/operating/operating_clinic',query:{transition:'def',time: new Date().getTime().toString()}}">
  	    <img
  			slot="icon"
  			slot-scope="props"
  			:src="props.active ? hospital.inactive : hospital.active"
  	    >
  	    <span>门诊</span>
  	</van-tabbar-item>
  	<van-tabbar-item replace :to="{path:'/operating/operating_user',query:{transition:'def',time: new Date().getTime().toString()}}">
  	    <span>我的</span>
  	    <img
  			slot="icon"
  			slot-scope="props"
  			:src="props.active ? my.inactive : my.active"
  	    >
  	</van-tabbar-item>
  </van-tabbar>
  </div>
</template>

<script>
import {mapActions,mapGetters} from 'vuex'
export default {
  name: 'operating',
  data(){
  	return{
      active: 0,
      index:{
          active: require('../../assets/image/shouye@2x.png'),
          inactive: require('../../assets/image/shouye-blue@2x.png')
      },
      hospital:{
          active: require('../../assets/image/Hospital@2x.png'),
          inactive: require('../../assets/image/Hospital-blue@2x.png')
      },
      my:{
          active: require('../../assets/image/wode@2x.png'),
          inactive: require('../../assets/image/wode-blue@2x.png')
      },
	  startLength:0,
	  overLength:0,
		startLengthY:0,
		overLengthY:0,
    }
  },
  props:['name'],
 
  computed:{
    // operatingReturnTopPage: {
    //   get: function() {
    //     // 
    //     return this.$store.state.operatingReturnTopPage;
    //   },
    //   set: function(newValue) {
    //     this.$store.state.operatingReturnTopPage = newValue;
    //   }
    // },
    ...mapGetters(['bottomShow','operatingReturnHomePage'])
  },
  created(){
      let thisVue = this
        $.ajax({
			  url:'/manager/login-refresh',
			  type:'post',
			  async:false,
			  success:function(res){
			    if(res.code == 0){
					  thisVue.$store.state.operating.login=res.data
			    }
			  }
      })
  },
  mounted(){
    window.addEventListener("scroll", this.handleScroll, true);
  },
   activated(){
    localStorage.setItem("entrance",3)
  },
  watch:{
    $route(to,from){
      // 
      //localStorage.setItem('lastRoute',JSON.stringify({name:to.name,query:to.query,params:to.params}))
    }
  },
  methods:{
		touchStartFn(_value){
			this.startLengthY = _value.changedTouches[0].screenY;
			this.startLength = _value.changedTouches[0].screenX
		},
		touchEndFn(_value){
			this.overLength = _value.changedTouches[0].screenX;
			this.overLengthY = _value.changedTouches[0].screenY
			if((this.overLength-this.startLength)>150 && (this.startLengthY - this.overLengthY) < 150){
				this.$router.back()
			}
		},
    // 滑动一定距离出现返回顶部按钮
    // handleScroll() {
    //   if(!this.$refs.operatingRef)
    //     return
    //   let scrollTop =
    //     this.$refs.operatingRef.scrollTop ||
    //     this.$refs.operatingRef.pageYOffset;
    //   let windowHeight =
    //     document.documentElement.clientHeight || this.$refs.operatingRef.clientHeight;
    //   let data =
    //     this.$refs.operatingRef.scrollHeight >
    //     (window.innerHeight || document.documentElement.clientHeight);
    //   // 
    //   let opacityValue =
    //     Math.round(
    //       ((scrollTop + windowHeight) / this.$refs.operatingRef.scrollHeight) * 100
    //     ) / 100;
    //   // 
    //   if (data && scrollTop > 800) {
    //     this.operatingReturnTopPage = true;
    //     this.$refs.returnTopRef.style.opacity = 1;
    //     this.$refs.returnHomePageRef.style.bottom = '1.5rem';
    //   } else {
    //     debugger
    //     this.$refs.returnTopRef.style.opacity = 0;
    //     this.$refs.returnHomePageRef.style.bottom = '1rem';
    //     this.operatingReturnTopPage = false;
    //   }
    // },
    // 返回列表顶部按钮
    // returnTopFn() {
		// debugger
    //   var scrollTop =
    //     this.$refs.operatingRef.scrollTop ||
    //     this.$refs.operatingRef.scrollTop ||
    //     this.$refs.operatingRef.pageYOffset;
    //   let windowHeight =
    //     document.documentElement.clientHeight || this.$refs.operatingRef.clientHeight;
    //   for (let i = 0; i < (scrollTop + windowHeight); i++) {
    //     var clearReturn = setTimeout(() => {
    //       this.$refs.operatingRef.scrollTop--;
    //       window.pageYOffset--;
    //       this.$refs.operatingRef.scroll--;
    //       document.documentElement.scrollTop--;
    //     }, 5);
    //   }
    //   this.$refs.returnHomePageRef.style.bottom = '.6rem';
    //   this.$refs.returnTopRef.style.opacity = 0;
    //   this.operatingReturnTopPage = false;
    // },
    // 返回首页按钮触发事件
    returnHomePageFn(){
      this.$router.replace({path:'/operating/operating_index',query:{transition:'def'}});
    },
  },
}
</script>

<style>
#operating {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  height: 100%;
  /* overflow-y: scroll; */
	/* touch-action: pan-y; */
	/* -webkit-overflow-scrolling: touch; */
  overflow: hidden;

}

.appView {
     /* position: absolute; */
     width: 100%;
     background: #fff;
     height: 100%;
     max-height: 100vh;
     min-height: 100vh;
     transition: transform 0.24s ease-out;
 }
 #app.quickback .appView{
     transition-duration: 10s;
 }
 .slide-left-enter {
     transform: translate(100%, 0);
 }
 .slide-left-leave-active {
     transform: translate(-50%, 0);
 }
 .slide-right-enter {
     transform: translate(-50%, 0);
 }
 .slide-right-leave-active {
     transform: translate(100%, 0);
 }
.returnHomePage{
  z-index: 99;
  position: fixed;
  right: 0.2rem;
  bottom: 1rem;
  opacity: 1;
  width: 0.4rem;
  height: 0.4rem;
  /* line-height: .4rem; */
  text-align: center;
  border-radius: 50%;
  background-color: rgba(255,255,255,.8);
  border: 1px solid #bfbebe;
}

.returnHomePage img {
  background: none;
  border: none;
  width: 0.18rem;
  display: block;
  margin: 0rem auto;
  margin-top: 0.045rem;
  /* height: .5rem; */
}

.returnHomePage span{
	display: block;
	font-size: 12px;
	transform:scale(0.85);
}
.returnTop {
  z-index: 99;
  position: fixed;
  right: 0.2rem;
  bottom: 1rem;
  opacity: 0;
  width: 0.4rem;
  height: 0.4rem;
  /* line-height: .4rem; */
  text-align: center;
  border-radius: 50%;
  background-color: rgba(255,255,255,.8);
  border: 1px solid #bfbebe;
}
.returnTop img {
  background: none;
  border: none;
  width: 0.18rem;
  display: block;
  margin: 0rem auto;
  margin-top: 0.045rem;
  /* height: .5rem; */
}

.returnTop span{
	display: block;
	font-size: 12px;
	transform:scale(0.85);
}
</style>
