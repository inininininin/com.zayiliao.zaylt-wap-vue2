<template>
	<div class="promotersSearch" ref='promotersSearchRef'>
		<div class="nav" :style="{'padding-top':$store.state.paddingTop}">
			<img src="../../../assets/image/shape@3x.png" alt="" @click="goBackFn"  id="navback">
			<div class="topNav">
				<img src="" alt="">
				<img src="../../../assets/image/sousuo@2x.png" alt="">
				<input type="text" @keyup.enter="searchFn" v-focus='true' v-model="searchInputValue">
				<span @click="searchFn">搜索</span>
			</div>
		</div>
		<div class="zhangwei" :style="{'padding-top':$store.state.paddingTop}"></div>
		<div class="promotersSearch_list" :style="{height: 'calc(100% - '+ (parseInt($store.state.paddingTop.replace('px',''))+47+'px)')}" @scroll="handleScroll" ref="promotersSearch_list"> 
			<!-- <van-pull-refresh v-model="pullingDown" @refresh="afterPullDown" style="ovflow:hidden"> -->
			<van-list v-model="loading" :finished="finished" finished-text="没有更多了" @load="onLoad">
				<ul>
					<li v-for="(item,inx) in promotersList" :key="inx" @click="$router.push({path:'/hospital/hospital_promotersDetails',query:{hospitalUserId: item.hospitalUserId,time: new Date().getTime().toString()}})">
					<!-- <router-link :to="{path : '/hospital/hospital_promotersDetails',query:{hospitalUserId: item.hospitalUserId,}}"> -->
						<div class="list">
						<img src="../../../assets/image/ren@2x.png" alt="">
						<h4>{{item.name}}</h4>
						<div class="listRight">
							<span>门诊数：{{item.clinicCount}}</span>
							<img src="../../../assets/image/right@2x.png" alt="">
						</div>
						</div>
					<!-- </router-link> -->
					</li>
				</ul>
			</van-list>
			<!-- </van-pull-refresh> -->
		</div>
		<div class="returnTop" @click="$refs.promotersSearch_list.scrollTop=0;hospitalReturnTopPage = false;" ref="returnTopRef" v-show="hospitalReturnTopPage">
			<img src="../../../assets/image/returnTop.png" alt />
			<span>顶部</span>
		</div>
	</div>
</template>

<script>
import axios from 'axios'
import {mapActions,mapGetters} from 'vuex'
import qs from 'qs';
export default {
	name: 'promotersSearch',
	data () {
		return {
			promotersList:[],
			searchInputValue : '',
			loading: true,
			finished: false,
			page: 0,
			query:'',
			scrollTop:0,
			hospitalReturnTopPage:false,
			pullingDown: false,
		}
	},
	computed:{
	},
	components:{
	},
	created(){
	},
	mounted () {
s	},
	activated() {
		if(this.query != JSON.stringify(this.$route.query)){
			this.initData()
			this.query = JSON.stringify(this.$route.query);
			if(window.plus){
				//plus.navigator.setStatusBarBackground("#ffffff");
				plus.navigator.setStatusBarStyle("dark")
			}
		}
		if(this.scrollTop != 0){
			this.$refs.promotersSearch_list.scrollTop = this.scrollTop;
		}
	},
	methods: {
		afterPullDown() {
			debugger
			setTimeout(() => {
				this.pullingDown = false;
				this.initData();
			}, 500);
		},
		initData(){
			Object.assign(this.$data, this.$options.data());
			this.onLoad()
		},
		// 滑动一定距离出现返回顶部按钮
		handleScroll() {
			this.scrollTop = this.$refs.promotersSearch_list.scrollTop || this.$refs.promotersSearch_list.pageYOffset
			if (this.scrollTop > 800) {
				this.hospitalReturnTopPage = true;
			} else {
				this.hospitalReturnTopPage = false;
			}
		},
		goBackFn(){
			this.$router.back()
		},
		onLoad(){
			++this.page;
			let data = {
				pn: this.page,
				ps: 10,
				kw:this.searchInputValue
			}
			this.getData(data);
		},
		getData(_data){
			this.$axios.get('/hospital/admin/hospital-users?'+'&'+qs.stringify(_data))
			.then(res => {
				if(res.data.codeMsg){
					this.$toast(res.data.codeMsg)
				}
				if(res.data.data.rows.length != 0){
					for(let i in res.data.data.rows){
						this.promotersList.push(res.data.data.rows[i])
						// console.dir(this.promotersList)
					}
					if(this.promotersList.length<10){
						let windowHeight = document.documentElement.clientHeight || document.body.clientHeight;
						// 
						this.$refs.promotersSearchRef.style.height = windowHeight+ 'px'
					}
					// 
					this.loading = false;
				}else {
					this.loading = false;
					this.finished = true;
				}
				
			})
			.catch((err)=>{})
		},
		searchFn(e){
		  this.promotersList = [];
		  this.getData({
			kw:this.searchInputValue
		  });
		}
	},
}
</script>

<style scoped>
.promotersSearch{
	width: 100%;
	height: 100%;
	background-color: #F5F5F5;
}
.nav{
	position: fixed;
	left: 0;
	right: 0;
	z-index: 999;
	margin: .08rem auto 0rem;
	width: 93.6%;
	height: .33rem;
	line-height: .33rem;
	background-color: #F5F5F5;
	padding-bottom: .12rem;
}
.nav>img{
	width: .09rem;
	height: .15rem;
	/* display: inline-block;	 */
	padding-right: .12rem;
}
.topNav{
	width: 93.6%;
	height: .33rem;
	line-height: .33rem;
	display: inline-block;
	background-color: #FFFFFF;
	border-radius: .15rem;
	float: right;
	position: relative;
}
.topNav img{
	width: .14rem;
	height: .15rem;
	/* padding-left: .14rem; */
	padding-right: .1rem;

}
.topNav span{
	width: .4rem;
	height: .16rem;
	line-height: .16rem;
	position: absolute;
	top: 0;
	bottom: 0;
	margin: auto 0;
	text-align: right;
	right: .17rem;
	border-left: 1px solid #C2C2C2;
	font-size: .14rem;
}
.topNav input{
	height: .21rem;
	width: 65.6%;
	border: none;
	font-size: .14rem;
	/* border-right: 2px solid #C2C2C2; */
}
.topNav span{
	color: #2B77EF;
}
.zhangwei{
	width: 100%;
	height: .42rem;
}
.promotersSearch ul{
	width: 100%;
	/* height: 100%; */
}
.promotersSearch ul li{
	width: 93.6%;
	height: .5rem;
	margin:.12rem auto 0;
	line-height: .5rem;
	overflow: hidden;
	background-color: #FFFFFF;
}
.promotersSearch ul li:first-child{
	margin-top: 0px;
}
.list{
	height: 100%;
	width: 100%;
	position: relative;
}
.list img:first-child{
	width: .17rem;
	height: .16rem;
	margin: 0rem .1rem 0rem .15rem;
	display: inline-block;
}
.list h4{
	color: #333333;
	font-size: .14rem;
	font-weight: bold;
	display: inline-block;
}
.listRight{
	position: absolute;
	right: .1rem;
	top:0;
	font-size: .13rem;
}
.listRight span{
	margin-right: .15rem;
}
.listRight img{
	height: .15rem;
}
.promotersSearch_list{
	/* height: calc(100% - .47rem); */
	touch-action: pan-y;
	-webkit-overflow-scrolling: touch;
	overflow: scroll;
	overflow-x: hidden;
	width: 100%;
}

</style>
