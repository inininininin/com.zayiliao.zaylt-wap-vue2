<template>
	<div class="caseDetails" ref="caseDetails" @scroll="handleScroll">
		<div class="topNav" :style="{'padding-top':$store.state.paddingTop}">
			<img src="../../../assets/image/shape@3x.png" alt="" @click="goBackFn"  id="navback">
			<img src="../../../assets/image/share@3x.png" @click="share" alt="">
		</div>
		<div class="zhangwei" :style="{'padding-top':$store.state.paddingTop}"></div>
		<div class="banner" v-show="!!caseInfo.cover">
			<img v-lazy="caseInfo.cover"  alt="">
		</div>
		<div class="content" @scroll="handleScroll" ref="content">
			<h3>{{caseInfo.name}}</h3>
			<p style="padding-top:13px;color: #999999;">浏览量：{{caseInfo.viewCount}}&nbsp;&nbsp;&nbsp;分享数：{{caseInfo.shareCount}}</p>
			<div class="headPortrait">
				<!-- <img src="../../../assets/image/logo@2x.png" alt=""> -->
				<img :src="caseInfo.hospitalCover? caseInfo.hospitalCover:'../../../assets/image/logo@2x.png'" alt="">
				<span>{{caseInfo.hosptialName}}</span>
				<span>{{moment(caseInfo.alterTime).format('YYYY-MM-DD HH:mm')}}</span>
			</div>
			<p v-html="caseInfo.content"></p>
		</div>
		<div class="returnTop" @click="$refs.caseDetails.scrollTop=0;hospitalReturnTopPage = false;" ref="returnTopRef" v-show="hospitalReturnTopPage">
			<img src="../../../assets/image/returnTop.png" alt />
			<span>顶部</span>
		</div>
	</div>
</template>

<script>
import qs from 'qs';
export default {
	name: 'caseDetails',
	data () {
		return {
			caseInfo:{
				addTime : '',
				alterTime : '',
				cover : '',
				hosptialpath : '/hospital/',
				path : '/hospital/',
				content:'',
				shareCount:"",
				viewCount:""
			},
			query:'',
			scrollTop:0,
    		hospitalReturnTopPage:false,
		}
	},
	computed:{
	},
	components:{
	},
	beforeCreate(){

	},
	created(){
  	  	let myDate = new Date();
    	this.caseInfo.alterTime = myDate.toLocaleDateString()
	},
 	mounted(){
	},
	activated() {
		if(window.plus){
			//plus.navigator.setStatusBarBackground("#ffffff");
			plus.navigator.setStatusBarStyle("dark")
		}
		if(this.query != JSON.stringify(this.$route.query)){
			Object.assign(this.$data, this.$options.data());
			this.query = JSON.stringify(this.$route.query)
			let postUrl = '';
			if(this.$route.query.data ==1){
				let postUrl ='/c2/article/item';
				this.getData(postUrl)
			}else{
				let postUrl ='/c2/project/item'
				this.getData(postUrl)
			}
		}
		if(this.scrollTop != 0){
			this.$refs.caseDetails.scrollTop = this.scrollTop;
		}
	},
	methods: {
		// 滑动一定距离出现返回顶部按钮
		handleScroll() {
			this.scrollTop = this.$refs.caseDetails.scrollTop || this.$refs.caseDetails.pageYOffset
			if (this.scrollTop > 800) {
				this.hospitalReturnTopPage = true;
			} else {
				this.hospitalReturnTopPage = false;
			}
		},
		share(){
		 	let shareUrl= location.href.replace('/hospital/hospital_caseDetails',"/sharePage")
		 	let vue = this
		 	this.$h5p.shareWeb(shareUrl+'/',this.caseInfo.cover,this.caseInfo.name,'',function(){
		 		vue.$axios.post('/c2/share')
		 	});
		},
		//回退方法
		goBackFn(){
			this.$router.back()
		},
		getData(url){
			this.$axios.post(url,qs.stringify({
				itemId : this.$route.query.itemId,
			}))
			.then(_d => {
				this.caseInfo = {
					addTime : _d.data.data.addTime,
					alterTime : _d.data.data.alterTime,
					cover : _d.data.data.cover,
					hosptialName : _d.data.data.hosptialName,
					name : _d.data.data.title?_d.data.data.title:_d.data.data.name,
					contentBtId : _d.data.data.contentBtId,
					hospitalCover:_d.data.data.hospitalCover,
					shareCount:_d.data.data.shareCount,
					viewCount:_d.data.data.viewCount,
				}
				this.$axios.get('/other/bigtxt/'+this.caseInfo.contentBtId+'/'+this.caseInfo.contentBtId)
				.then(_d => {
					this.$set(this.caseInfo,'content',_d.data)
				})
				.catch((err)=>{
				})
			})
			.catch((err)=>{
			})
		}
	},
}
</script>

<style scoped>
.caseDetails{
	width: 100%;
	height: 100%;
  	touch-action: pan-y;
	-webkit-overflow-scrolling: touch;
	overflow: scroll;
	overflow-x: hidden;
}
.topNav{
	width: 100%;
	height: .47rem;
	line-height: .47rem;
	position: fixed;
	top: 0;
	z-index: 999;
	background: #FFFFFF;
}
.zhangwei{
	width: 100%;
	height: .47rem;
}
.topNav img:first-child{
	width: .09rem;
	height: .15rem;
	float: left;
	margin-top: .16rem;
	margin-left: .16rem;
}
.topNav img:last-child{
	width: .19rem;
	height: .19rem;
	float: right;
	margin-top: .14rem;
	margin-right: .21rem;
}
.banner{
	height: 1.75rem;
	width: 100%;
}
.banner img{
	width: 100%;
	height: 1.75rem;
	object-fit: cover;
}
.content{
	width: 91.46%;
	height: .33rem;
	margin: 0 auto;
	margin-top: .22rem;
}
.content h3{
	color: #333333;
	font-size: .16rem;
	font-weight: bold;
}
.headPortrait{
	margin-top: .08rem;
	margin-bottom: .15rem;
}
.headPortrait img{
	width: .33rem;
	height: .33rem;
	float: left;
	margin-right: .1rem;
	border-radius: 50%
}
.headPortrait span{
	display: block;
	color: #333333;
	font-weight: bold;
}
.headPortrait span:last-child{
	color: #999999;
	font-weight: normal;
}
</style>
