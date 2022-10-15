<template>
	<view class="page-content">
		<pc-topbar @loginResult="loginResult"></pc-topbar>
		<scroll-view 
		class="lists" scroll-y="true" 
		:style="'height:'+listHeight+'px'"
		>
		<view class="page-content">
			<div class="video-content common-min-width common-flex">
			    
			<div class="video-left common-size-border common-fixed">
			    
			    <div class="nav-list common-mar-t-big">
			        <div class="common-flex common-center-y">
			            <img class="nav-list-icon" src="../../../static/pc/type.png" alt="">
			            <span class="common-text-f16 common-color-fff">全部分类</span>
			        </div>
			        <ul class="clearfix common-text-center common-mar-t-min">
						<block v-for="item in liveclass" :key="item">
							<li class="nav-item common-text-center common-left" @click="toListPage(item.name,'class',item.id)" v-if="item.id >0">
								<a class="common-block common-text-f14 common-color-fff">{{item.name}}</a>
							</li>
						</block>
			            
			        </ul>
			        <div class="common-absolute common-text-center common-mar-t-big common-absolute playing-box" style="width: 100%;left: 0px;">
			            <img class="code-img" src="../../../static/appqr.png" alt="">
			        </div>
			    </div>
			</div>
			
			    <div class="video-right common-mar-r-sm common-size-border">
			
			        <!--推荐直播-->
			        <div class="recommond-video common-size-border common-mar-t-big common-mar-b-sm">
			            <div class="common-flex common-center-y">
			                <span class="common-mar-l-min common-text-f24 common-text-bold common-color-23272D">{{title}}</span>
			            </div>
			            <ul class="clearfix list-video beautiful-list" id="list">
							<block v-for="item in lists" :key="item">
								<pc-live-itemnew imgWidth="196" :item="item"></pc-live-itemnew>
							</block>
							
			            </ul>
			        </div>
			        <div class="no-list common-text-center" v-if="(!lists || lists.length == 0) && isnomore">
			            <img src="../../../static/pc/noplaying.png" alt="">
			            <p class="common-text-center common-text-f14 common-color-808998">暂时没有开播的人~</p>
			        </div>
			    </div>
			</div>
		</view>
		</scroll-view>
	</view>
</template>

<script>
	import mIcon from '@/components/m-icon/m-icon.vue';
	const app = getApp();
	export default {
		data() {
			return {
				type:"",
				lists:[],
				listHeight:500,
				liveclass:[],
				title:"",
				classId:0,
				page:1,
				isnomore:false,
			}
		},
		onLoad(e) {
			this.title = e.title;
			this.classId = e.class_id;
			this.type = e.type;
			let sysInfo = uni.getSystemInfoSync()
			this.windowWidth = sysInfo.screenWidth
			this.windowHeight = sysInfo.windowHeight-80
			this.listHeight = sysInfo.windowHeight-70;
			this.getClass();
			this.initList();
		},
		methods: {
			toListPage(title,type,classId){
				this.title = title;
				this.type = type;
				this.classId = classId;
				this.initList();
				// uni.navigateTo({
				// 	url:"/pages/pc/list/list?title="+title+"&type="+type+"&class_id="+classId
				// })
			},
			loginResult(){
				console.log("loginResult");
			},
			initList(){
				this.isnomore = false;
				this.page = 1;
				this.lists = [];
				this.getList();
			},
			getList(){
				if(!this.type){
					this.type = "host";
					this.title = "热门";
				}
				switch (this.type){
					case "class":
						this.getClassList();
						break;
					case "host":
						this.getHostList();
						break;
					case "recommend":
						this.getRecommendList();
						break;
					default:
						this.isnomore = true;
						break;
				}
			},
			getClass(){
				uni.request({
					url: app.globalData.site_url + '/appapi/?service=Home.getConfig',
					method: 'GET',
					data: {},
					success: res => {
						let liveclass = res.data.data.info[0].liveclass;
						this.liveclass = liveclass;
					},
					fail: () => {},
					complete: () => {}
				});
			},
			getHostList(){
				// 热门主播
				
				if(this.isnomore){
					return;
				}
				let header = {};
				//header['content-type'] = 'application/json';
				uni.request({
					url: app.globalData.site_url + '/appapi/?service=Home.gethot',
					method: 'GET',
					data: {
						p:this.page,
					},
					success: res => {
						
						let list = res.data.data.info[0].list;
						// console.log(res);
						if(!this.lists || this.lists.length == 0){
							this.lists = list;
						}else if(list){
							this.lists = this.lists.concat(list);
						}
						
						if(!list || list.length == 0){
							this.isnomore = true;
						}else{
							this.page = this.page + 1;
						}
					},
					fail: () => {},
					complete: () => {}
				});
			},
			getRecommendList(){
				// 推荐主播
				
				if(this.isnomore){
					return;
				}
				let header = {};
				uni.request({
					url: app.globalData.site_url + '/appapi/?service=Home.GetRecommendLive',
					method: 'GET',
					data: {
						p:this.page,
					},
					success: res => {
						
						let list = res.data.data.info.list;
						// console.log(res);
						if(!this.lists || this.lists.length == 0){
							this.lists = list;
						}else if(list){
							this.lists = this.lists.concat(list);
						}
						
						if(!list || list.length == 0){
							this.isnomore = true;
						}else{
							this.page = this.page + 1;
						}
					},
					fail: () => {},
					complete: () => {}
				});
			},
			getClassList(){
				if(this.isnomore){
					return;
				}
				let header = {};
				//header['content-type'] = 'application/json';
				uni.request({
					url: app.globalData.site_url +'/appapi/?service=Home.getClassLive&liveclassid='+this.classId+'&p='+this.page,
					method: 'GET',
					dataType: 'json',
					header,
					data: {
						service:"Home.getClassLive",
						liveclassid:this.classId,
						p:this.page,
					},
					success: res => {
						// console.log(res);
						if(!this.lists || this.lists.length == 0){
							this.lists = res.data.data.info;
						}else if(res.data.data.info){
							this.lists = this.lists.concat(res.data.data.info);
						}
						// console.log(this.lists);
						
						if(!res.data.data.info || res.data.data.info.length == 0){
							this.isnomore = true;
						}else{
							this.page = this.page + 1;
						}
						
						if(this.isnomore){
							console.log("isnomore");
						}else{
							console.log("isnomore false");
						}
					},
					fail: () => {},
					complete: () => {}
				});
			},
			
		}
	}
</script>

<style>
	@import url("@/common/pc/rebo-common.css");
	@import url("@/common/pc/rebo-index.css");
	@import url("@/common/pc/rebo-video.css");
	.video-content .video-left{
		z-index: unset;
	}
</style>
