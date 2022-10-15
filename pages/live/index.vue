<template>
	<view class="content">
	<!-- 列表 -->
	<scroll-view class="lists" scroll-y="true" :scroll-top="scrollTop"  @scrolltoupper="upper" @scrolltolower="lower">
		<view class="lists_li" v-for="(item,lists) in lists" :key="item.uid" @click="golive(item)">
			<image :src="item.thumb" mode=""></image>
			<view class="info">
				<image src="../../static/index/ordinary.png" mode="" class="room"></image>
				<view class="user_info">
					<view class="user">
						<image :src="item.avatar_thumb"  class="portrait" mode="" ></image>
						<text>{{item.user_nicename}}</text>
					</view>
				</view>
				<view class="ren">
					<image src="../../static/ren.png" mode="" class="ren_coin"></image>
					<text>{{item.nums}}</text>
				</view>
			</view>
		</view>
	</scroll-view>
	
	<!-- 底部导航 -->
	<view style="height: 10vh;">
	    <!-- 底部tab样式 -->
	    <view class="tab">
	        <!-- 循环 tabbar里面的数据 -->
	        <block v-for="(item,index) in tabbar" :key="index">
	            <view class="tab_tiem" :class="[current == index ? 'active': '']" :data-index="index">
					<navigator :url="item.href" class="tab_tiem_nav">
	                <view class="item_img">
	                    <image class="image" :src="current != index ? item.img :item.slectImg " mode=""></image>
	                </view>
	                  <view class="item_name">
	                    {{item.text}}
	               </view>
				   </navigator>
	            </view>
				
	        </block>
	    </view>
	</view>
	</view>
</template>
<style>
	.lists_li{
		background-color: #eee;
	}
</style>
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
				tabbar: [{
						img: '../../static/icon_1.png',  //未选中
						slectImg: '../../static/icon_01.png', //已选中
						text: '首页',
						href: '../index/index',
					},
					{
						img: '../../static/icon_2.png',
						slectImg: '../../static/icon_02.png',
						text: '直播',
						href: '../live/index',
					},
					// {
					// 	img: '../../static/icon_3.png',
					// 	slectImg: '../../static/icon_03.png',
					// 	text: '短视频',
					// 	href: '../video/index',
					// },
					{
						img: '../../static/icon_4.png',
						slectImg: '../../static/icon_04.png',
						text: '我的',
						href: '../my/index',
					},
				],
			}
		},
		onLoad() {
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
			uni.request({
				url: app.globalData.site_url +'/appapi/?service=Home.gethot',
				method: 'GET',
				data: {},
				success: res => {
					this.slide=res.data.data.info[0].slide;
					this.lists=res.data.data.info[0].list;
					console.log(this.lists);
				},
				fail: () => {},
				complete: () => {}
			});
			
		},
		methods: {
			lower(e){
				console.log(e);
			},
			golive(e){
				uni.navigateTo({
					url:'../live/livepull?liveuid='+e.uid+'&stream='+e.stream+'&anyway='+e.anyway+'&live_nicename='+e.user_nicename+'&live_avatar='+e.avatar_thumb,
				});
			},
		}
	}
</script>


<style>
	.content {
		width: 100%;
		padding-top: 1%;
		display: flex;
		flex-direction: column;
		align-items: center;
	}

	/* 直播列表 */
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
		width: 49%;
		height: 10rem;
		margin: 0 0.5%;
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
