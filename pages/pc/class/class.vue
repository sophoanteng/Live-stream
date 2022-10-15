<template>
	<view class="page-content">
		<pc-topbar @loginResult="loginResult"></pc-topbar>
		<scroll-view 
		class="lists" scroll-y="true" 
		:style="'height:'+listHeight+'px'"
		>
		<view class="page-content">
			
			<view class="g-box feed-list mg-t-20  bg-white" id="hot">
				<view class="box-hd">
					<view class="box-title">
					<text class="icon">
					<image src="../../../static/pc/remen.png" class="title-img"></image>
					</text>{{title}}</view>
				</view>
				<view class="box-bd">
					<view class="list">
						<block v-if="lists && lists.length > 0"  >
							<pc-live-item :item="item" v-for="item in lists"></pc-live-item>
						</block>
						<block v-else="isnomore">
							<view class="nomore-tip mg-b-20">
								暂无栏目数据
							</view>
						</block>
					</view>
				</view>
			</view>
			
		</view>
		</scroll-view>
		<pc-footer></pc-footer>
	</view>
</template>

<script>
	import mIcon from '@/components/m-icon/m-icon.vue';
	const app = getApp();
	export default {
		data() {
			return {
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
			this.classId = e.id;
			let sysInfo = uni.getSystemInfoSync()
			this.windowWidth = sysInfo.screenWidth
			this.windowHeight = sysInfo.windowHeight-80
			this.listHeight = sysInfo.windowHeight-80-50;
			this.initList();
		},
		methods: {
			toLogin(){
				uni.navigateTo({
					url:"/pages/pc/login/login"
				})
			},
			loginResult(){
				console.log("loginResult");
			},
			initList(){
				this.isnomore = false;
				this.page = 1;
				this.getList();
			},
			
			getList(){
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
						console.log(res);
						if(!this.lists || this.lists.length == 0){
							this.lists = res.data.data.info;
						}else if(res.data.data.info){
							this.lists = this.lists.concat(res.data.data.info);
						}
						console.log(this.lists);
						
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
			}
		}
	}
</script>

<style>
	
	
	@import url("@/common/pc/common.css");
	@import url("@/common/pc/index.css");
	.nomore-tip{
		margin-top: 100rpx;
		text-align: center;
		color: #aaa;
		font-size: 28rpx;
	}
	.lists_li_img,.live_class_li_img{
		background-color: #eee;
	}

	.live_class{
		width: 100%;
		margin-top: 0.5rem;
		display: flex;
		flex-wrap: wrap;
		overflow-y: hidden;
		overflow-x: scroll;
	}
	.live_class::-webkit-scrollbar {
	  display: none;
	}
	.live_class_li{
		float: left;
		width: 10%;
		height: auto;
		text-align: center;
		margin: 20rpx 0px;
	}
	
	.live_class_li>image{
		height: 100rpx;
		width: 100rpx;
		border-radius: 100%;
	}
	.live_class_li .live_class_name{
		width: 100%;
		height: 60rpx;
		line-height: 60rpx;
		font-size: 28upx;
		color: #353535;
	}
	
	#doc-hd.double {
	    background: #f2f2f2;
	    border-bottom: 1px solid #e4e4e4;
	    margin-bottom: 0px;
	    -moz-box-shadow: 0px 2px 4px rgba(0, 0, 0, .10);
	    -webkit-box-shadow: 0px 2px 4px rgb(0 0 0 / 10%);
	    box-shadow: 0px 2px 4px rgb(0 0 0 / 10%);
	}
	
	#doc-hd.double .topbar {
	    height: 62px;
	    background: #FFF;
	    border-bottom: 0px solid #fc4e53;
	}
	
	.page-content {
	    margin-top: 8px;
	    position: relative;
	    z-index: 1;
	    margin-bottom: 90px;
		width: 1200px;
		margin: 0 auto;
	}
	
	.box-hd .title-img{
		width: 30px;
		height: 30px;
	}
</style>
