<template>
	<div class="hospital">
		<div class="topNav" :style="{'padding-top':$store.state.paddingTop}">
      <!-- <router-link :to="{path : '/outpatient/outpatient_articleSearch'}"> -->
        <div class="hospital_search" @click="$router.push({path:'/outpatient/outpatient_articleSearch',query:{time: new Date().getTime().toString()}})">
        	<input type="text" placeholder="搜索文章">
        	<img src="../../../assets/image/sousuo@2x.png" alt="">
        </div>
      <!-- </router-link> -->

			<div class="clinic_buttton">
				<button>搜索</button>
			</div>
			<!-- <router-link :to="{path : '/outpatient/outpatient_clinicMessage',query:{}}"> -->
			<div class="hospital_information" @click="$router.push({path:'/outpatient/outpatient_clinicMessage',query:{time: new Date().getTime().toString()}})">
				<img src="../../../assets/image/xiaoxi@2x.png" alt="">
				<div class="num" v-if="this.$store.state.outpatient.login.newMessageCount == 0? true:false">
					<span>{{this.$store.state.outpatient.login.newMessageCount}}</span>
				</div>
			</div> 
			<!-- </router-link> -->
		</div>

		<div class="shared">
			<!-- <h3>共享医连体</h3> -->
			<ul>
				<!-- <router-link :to="{path : '/outpatient/outpatient_hospitalImage',query:{}}"> -->
				<li @click="$router.push({path:'/outpatient/outpatient_hospitalImage',query:{time: new Date().getTime().toString()}})">
					<img src="../../../assets/image/yiyuanxingxiang@2x.png" alt="">
					<span>医院形象</span>
				</li>
				<!-- </router-link> -->
				<!-- <router-link :to="{path : '/outpatient/outpatient_case',query:{}}"> -->
				<li @click="$router.push({path:'/outpatient/outpatient_case',query:{time: new Date().getTime().toString()}})">
					<img src="../../../assets/image/youzhianli@2x.png" alt="">
					<span>优质案例</span>
				</li>
				<!-- </router-link> -->
				<!-- <router-link :to="{path : '/outpatient/outpatient_expertsIntroduction',query:{}}"> -->
				<li @click="$router.push({path:'/outpatient/outpatient_expertsIntroduction',query:{time: new Date().getTime().toString()}})">
					<img src="../../../assets/image/zhuanjia@2x.png" alt="">
					<span>专家介绍</span>
				</li>
				<!-- </router-link> -->
				<!-- <router-link :to="{path : '/outpatient/outpatient_activityReleased',query:{}}"> -->
				<li @click="$router.push({path:'/outpatient/outpatient_activityReleased',query:{time: new Date().getTime().toString()}})">
					<img src="../../../assets/image/huodongfabu@2x.png" alt="">
					<span>最新活动</span>
				</li>
				<!-- </router-link> -->
			</ul>
		</div>
		<div class="article">
			<div class="articleTitle">
				<!-- <img src="../../../assets/image/Combined Shape@2x.png" alt=""> -->
				<h3>文章分享</h3>
			</div>
			<div class="articleList" @scroll="handleScroll" ref="articleList">
				<ul :model="article">
					<van-list  v-model="loading" :finished="finished" finished-text="没有更多了"  @load="onLoad">
						<li v-for="(items,inx) in article" :key="inx" @click="$router.push({path:'/outpatient/outpatient_caseDetails',query:{itemId : items.itemId,data: 1,time: new Date().getTime().toString()}})">
							<!-- <router-link :to="{path : '/outpatient/outpatient_caseDetails' ,query : {itemId : items.itemId,data: 1,}}"> -->
							<div class="article_left" :style="{width:items.img?'60.1%':'100%'}">
								<p>{{items.content}}</p>
								<div class="article_leftTime">
									<img src="../../../assets/image/time@2x.png" alt="">
									<span>{{moment(items.time).format('YYYY-MM-DD HH:mm')}}</span>
								</div>
							</div>
							<div class="article_right" v-if="items.img? true:false">
								<img :src=items.img alt="">
							</div>
							<!-- </router-link> -->
						</li>
					</van-list>
				</ul>
			</div>
		</div>
		<div class="returnTop" @click="$refs.articleList.scrollTop=0;hospitalReturnTopPage = false;" ref="returnTopRef" v-show="hospitalReturnTopPage">
			<img src="../../../assets/image/returnTop.png" alt />
			<span>顶部</span>
		</div>
	</div>
</template>

<script>
import qs from 'qs';
export default {
	name: 'hospital',
	data () {
		return {
			name: 'hospital',
			article:[],
			loading: true,
			finished: false,
			page:0,
			hospitalReturnTopPage:false,
			scrollTop:0
		}
	},
	computed:{
	},
	components:{

	},
	created () {

	},
 	mounted() {
 	 },
  activated(){
		if(this.query != JSON.stringify(this.$route.query)){
			this.initData()
			this.query = JSON.stringify(this.$route.query);
			if(window.plus){
				//plus.navigator.setStatusBarBackground("#ffffff");
				plus.navigator.setStatusBarStyle("dark")
			}
		}
		if(this.scrollTop != 0){
			this.$refs.articleList.scrollTop = this.scrollTop;
		}
    },
  	methods: {
	  // 滑动一定距离出现返回顶部按钮
		handleScroll() {
			this.scrollTop = this.$refs.articleList.scrollTop || this.$refs.articleList.pageYOffset
			if (this.scrollTop > 800) {
				this.hospitalReturnTopPage = true;
			} else {
				this.hospitalReturnTopPage = false;
			}
		},
	  	initData(_data){
		 	let thisVue = this
			if(this.$route.meta.auth && !this.$store.state.outpatient.login)
			this.$toast({message:'请登录',onClose:function(){
				thisVue.$router.replace({ path : '/outpatientLogin',query:{time:1}});
			}})
			Object.assign(this.$data, this.$options.data());
			this.onLoad()
			
		},
		getdata(){
			this.$axios.post('/c2/article/items',qs.stringify({
				hospitalId : this.$store.state.outpatient.login.hospital.hospitalId,
				clinicId : this.$store.state.outpatient.login.clinic.clinicId,
				pn : this.page,
				ps : 10
			}))
			.then(res => {
				if(res.data.codeMsg){
					this.$toast(res.data.codeMsg)
				}
				if(res.data.data.items.length != 0){
					for(let i in res.data.data.items){
						if(res.data.data.items[i]){
							this.article.push({
								content:res.data.data.items[i].title,
								img: res.data.data.items[i].cover,
								time:res.data.data.items[i].alterTime,
								itemId: res.data.data.items[i].itemId,
							})
						}
					}
					debugger
					// 加载状态结束
					this.loading = false;
				}else{
					this.loading = false;
					this.finished = true;
				}
			})
			.catch((err)=>{
				this.$toast(err)
			})
		},
		onLoad(){
			this.page++;
			this.getdata()
		},
  	},
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.hospital{
	width: 100%;
	background: #fff;
	height: 100%;
	overflow: hidden;
}
.topNav{
	width: 100%;
	height: .42rem;
}
.hospital_search{
	float:left;
	width: 73%;
	position: relative;
}
.hospital_search input{
	margin: .08rem 0rem 0rem .16rem;
	height:.34rem;
	width: 78%;
	border: none;
	border-radius: .33rem;
	padding-left: 11.6%;
	background-color: rgba(0, 0, 0, 0.05);
}
.hospital_search img{
	width: .14rem;
	height: .15rem;
	position: absolute;
	top: .18rem;
	left: 9.8%;
}
.clinic_buttton{
	float: left;
	margin-top: .125rem;
	margin-left: -.05rem;
}
.clinic_buttton button{
	color: #FFFFFF;
	background-color: #2B77EF;
	border-radius: .15rem;
	border: none;
	height: .28rem;
	width: .5rem;
	font-size: .12rem;
}
.hospital_information{
	float:left;
	width: 10.3%;
	margin-left: .14rem;
	margin-top: .15rem;
}
.hospital_information img{
	width: .19rem;
	height: .24rem;
}
.shared{
	height: .79rem;
	width: 89%;
	margin: 0rem auto;
	margin-top: .3rem;
}
.shared h3{
	font-weight: bold;
	font-family: '苹方-简 中粗体';
}
.shared ul{
	width: 100%;
	margin-top: .25rem;
}
.shared ul li{
	width: 25%;
	float: left;
	text-align: center;
}
.shared ul li img{
	width: .56rem;
	height: .43rem;
	display: block;
	margin: 0 auto;
	margin-bottom: .12rem;
}
.article{
	width: 100%;
	height: calc(100% - 2.15rem);
}
.articleList{
	height: calc(100% - .24rem);
	touch-action: pan-y;
	-webkit-overflow-scrolling: touch;
  	overflow: scroll;
  	overflow-x: hidden;
}
.articleTitle{
	/* height: 100%; */
	width: 91.5%;
	margin: 0rem auto;
	margin-top: .2rem;
}
.articleTitle img{
	float: left;
	width: .18rem;
	height: .21rem;
	margin-right: .12rem;
}
.articleTitle h3{
	font-weight: bolder;
	font-size: .16rem;

}
.article ul{
	margin-top: .2rem;
	width: 91.5%;
	margin: 0rem auto;
}
.article ul li {
	width: 100%;
	height: .97rem;
	border-bottom:1px solid #D8D8D8 ;
	margin: .12rem 0;
}
.article ul li:last-child{
	border: none;
}
.article_left{
	float: left;
	width: 60.1%;
}
.article_left p{
	font-size: .14rem;
	font-weight: bold;
	display: -webkit-box;
	overflow: hidden;
	text-overflow: ellipsis;
	word-wrap: break-word;
	-webkit-line-clamp: 2;
	-webkit-box-orient: vertical;
	height: .42rem;
}
.article_leftTime{
	margin-top: .23rem;
	height: .16rem;
	line-height: .16rem;
	position: relative;
}
.article_leftTime img{
	position: absolute;
	top:.02rem;
	width: .11rem;
	height: .11rem;

}
.article_leftTime span{
	margin-left: .17rem;
}
.article_right{
	float: left;
	margin-left: 7.8%;
	width: 32.1%;
}
.article_right img{
	width: 1.08rem;
	height: .85rem;
	object-fit: cover;
}
</style>
