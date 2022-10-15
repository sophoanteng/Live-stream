<template>
	<view class="content">
	<!-- 列表 -->
	<scroll-view class="lists" scroll-y="true" :scroll-top="scrollTop"  @scrolltoupper="upper" @scrolltolower="lower">
		<block v-if="lists && lists.length > 0">
		<view class="lists_li" v-for="(item,lists) in lists" :key="item.uid" @click="golive(item)">
			<image class="lists_li_img" :src="item.thumb" mode="" :data-src="item.thumb"></image>
			<view class="info">
				<image src="../../../static/index/ordinary.png" mode="" class="room"></image>
				<view class="user_info">
					<view class="user">
						<image :src="item.avatar_thumb"  class="portrait" mode="" ></image>
						<text>{{item.user_nicename}}</text>
					</view>
				</view>
				<view class="ren">
					<image src="../../../static/ren.png" mode="" class="ren_coin"></image>
					<text>{{item.nums}}</text>
				</view>
			</view>
		</view>
		</block>
		<block v-else="isnomore">
			<view class="nomore-tip">
				暂无栏目数据
			</view>
		</block>
	</scroll-view>
	
	</view>
</template>

<script>
	const app = getApp();
	export default {
		data() {
			return {
				scrollTop: 0,
				liveclass:[],
				slide:[],
				lists:[],
				current: 1, 
				classId:0,
				page:1,
				isnomore:false,
			}
		},
		onLoad(e) {
			//判断是否登入
			var res = global.isLogin();
			if(!res){
				uni.showModal({
					title:'请登录',
					content:"请登录",
					success:function(){
						uni.navigateTo({
							url:'../login/login'
						});
					}
				})
			}
			this.classId = e.id;
			uni.request({
				url: app.globalData.site_url +'/appapi/?service=Home.getConfig',
				method: 'GET',
				data: {},
				success: res => {
					this.liveclass=res.data.data.info[0].liveclass;
				},
				fail: () => {},
				complete: () => {}
			});
			uni.setNavigationBarTitle({
				"title":e.title
			})
			this.initList();
		},
		onPullDownRefresh(){
			this.initList();
		},
		onReachBottom() {
			this.getList();
		},
		methods: {
			lower(e){
				console.log(e);
			},
			golive(e){
				uni.navigateTo({
					url:'../livepull?liveuid='+e.uid+'&stream='+e.stream+'&anyway='+e.anyway+'&live_nicename='+e.user_nicename+'&live_avatar='+e.avatar_thumb,
				});
			},
			
			initList(){
				this.isnomore = false;
				this.page = 1;
				this.lists = [];
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
						uni.stopPullDownRefresh();
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
					},
					fail: () => {},
					complete: () => {}
				});
			}
		}
	}
</script>


<style>
	.content {
		width: 100%;
		padding-top: 1%;
		display: flex;
		flex-direction: column;
		padding: 10px;
	}
	
	/* 直播列表 */
	.lists_li_img{
		background-color: #eee;
	}
	.nomore-tip{
		margin-top: 100rpx;
		text-align: center;
		color: #aaa;
		font-size: 28rpx;
	}
	.content .list_tit{
		width: 96%;
		padding: 0 2%;
		height: 3rem;
		line-height: 3rem;
	}
	.content .list_tit image{
		width: 1.5rem;
		height: 1.5rem;
		margin-right: 0.5rem;
		position: relative;
		top: 0.2rem;
	}
	.content .lists{
		width: 99%;
		padding: 0 0.5%;
	
	}
	.content .lists .lists_li{
		width: 48%;
		height: 10rem;
		margin: 5px 1%;
		margin-bottom: 1%;
		position: relative;
		float: left;
	}
	.content .lists .lists_li .info{
		position: absolute;
		bottom: 5%;
		width: 90%;
		left: 5%;
		color: #fff;
	}
	.content .lists .lists_li .info .room{
		width: 60px;
		height: 20px;
	}
	.content .lists .lists_li image{
		width: 100%;
		height: 100%;
		border-radius: 0.6rem;
	}
	.content .lists .lists_li .info .user_info{
		width:60%;
	}
	.content .lists .lists_li .info .user_info .user{
		width: 100%;
		height: 30px;
		line-height: 30px;
		font-size: 0.9rem;
		overflow: hidden
	}
	.content .lists .lists_li .info .user_info .user .portrait{
		width: 40rpx;
		height: 40rpx;
		border-radius: 100%;
		margin-right: 2%;
		position: relative;
		top: 8rpx;
	}
	.ren{
		width: 35%;
		overflow: hidden;
		position: absolute;
		bottom: 8%;
		font-size: 0.8rem;
		right: 0;
		text-align: right;
	}
	.ren .ren_coin{
		width: 30upx !important;
		height: 34upx !important;
		border-radius: 0upx !important;
		position: relative;
		top: 2upx;
		margin-right: 6upx;
	}
	
	.tab {
		width: 100%;
		position: fixed;
		display: flex;
		align-items: center;
		justify-content: space-between;
		z-index: 1024;
		background-color: #FFFFFF;
		height: 100rpx;
		left: 0;
		bottom: 0;
		padding-bottom: env(safe-area-inset-bottom);
		border-top: 1rpx solid #888888;
	
	}
	.tab_tiem {
		flex: 1;
		width: 25%;
		display: flex;
		align-items: center;
		flex-direction: column;
		justify-content: center;
		font-size: 24rpx;
		color: #666;
		height: 80rpx;
	}
	.tab_tiem_nav{
		text-align: center;
	}

	.item_img {
		padding-top: 4rpx;
	}

	.image {
		height: 30rpx;
		width: 30rpx;
	}

	.tab::before {
		color: red;
	}

	.item_name {
		font-weight: bold;
		transform: scale(0.8);
		transform-origin: center 100%;
		line-height: 30rpx;
	}

	.active {
		color: red;
	}
	

</style>
