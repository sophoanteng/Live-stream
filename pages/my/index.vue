<template>
	<view class="content" :style="'height:'+this.windowHeight+'px'">
		<view class="user_top">
			<image :src="avatar" mode="" class="active"></image>
			<view class="info">
				<view class="name">{{user_nicename}}</view>
				<view class="user_icon">
					<image src="../../static/man.png" mode="" class="sex" v-if="sex==1"></image>
					<image src="../../static/woman.png" mode="" class="sex" v-else></image>
				</view>
				<view class="userid">ID:{{uid}}</view>
			</view>
			<navigator url="../my/save" class="user_save">
				<image src="../../static/my/right.png" mode="" class="right"></image>
			</navigator>
		</view>
		
		<view class="list">
			<view class="li">
				<navigator url="../my/profit" class="li_nav">
					<image src="../../static/my/profit.png" mode="" class="li_icon"></image>
					<view class="li_tit">我的收益</view>
					<image src="../../static/my/list_more.png" mode="" class="list_more"></image>
				</navigator>
			</view>	
		</view>
		
		<view class="out" @click="out">退出登录</view>
		
		
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

<script>
	const app = getApp();
	export default {
		data() {
			return {
				uid:'',
				token:'',
				avatar:'',
				user_nicename:'',
				sex:1,
				current: 3, 
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
			this.windowHeight=uni.getSystemInfoSync().windowHeight;
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
			
			this.uid=res[0];
			this.token=res[1];
		
			uni.request({
				url: app.globalData.site_url +'/appapi/?service=User.getBaseInfo',
				method: 'GET',
				data: {
					uid:this.uid,
					token:this.token,
				},
				success: res => {
					var data=res.data.data;
					if(data.code!=0){
						uni.showToast({
							title: data.msg,
							icon:'none',
							duration: 2000
						});
						return 1;
					}
					this.avatar=data.info[0]['avatar'];
					this.user_nicename=data.info[0]['user_nicename'];
					this.sex=data.info[0]['sex'];					
				},
				fail: () => {},
				complete: () => {}
			});
			
			
		},
		methods: {
			out(){
				uni.showModal({
					title:'提示',
					content:"您确定退出吗？",
					success:function(){
						uni.removeStorageSync('uid');
						uni.removeStorageSync('token');
						uni.navigateTo({
							url:'../login/login'
						});
					}
				});
				
			}
		}
	}
</script>

<style>
	.content {
		width: 100%;
		background-color: #f8f8f8;
	}
	.content .user_top{
		width: 100%;
		padding: 20px 10%;
		/* height: 6rem; */
		position: relative;
		background-size: 100% 100%;
		background-image: url(../../static/my/my_bg.png);
	}
	.content .user_top .active{
		width:160upx;
		height: 160upx;
		border-radius: 100%;
		margin-top: 0.8rem;
	}
	.content .user_top .sex{
		width:52upx;
		height: 40upx;
	}
	.content .user_top>.info{
		position: absolute;
		top: 1.5rem;
		left: 7rem;
		color: #fff;
	}
	.content .user_top>.info>.name{
		height: 2rem;
		line-height: 2rem;
		font-size: 1.15rem;
	}
	.content .user_top>.info>.userid{
		height: 2rem;
		line-height: 2rem;
		font-size: 1rem;
	}
	.content .user_top>.user_save>.right{
		position: absolute;
	    top: 3rem;
	    right: 1.5rem;
	    width: 30px;
	    height: 30px;
	}
	.content .list{
		width: 100%;
		background-color: #fff;
		position: relative;
		padding: 0 4%;
		margin-top: 0.5rem
	}
	.content .list .li{
		width: 100%;
		height: 3rem;
		position: relative;
		line-height: 3rem;
	}
	
	.content .list .li .li_icon{
		width: 60upx;
		height: 60upx;
		position: absolute;
		top: 0.7rem;
	}
	
	.content .list .li .li_tit{
		width: calc(100% - 80upx);
		margin-left: 80upx;
	}
	
	.content .list .li>.li_nav>.list_more{
		position: absolute;
	    top: 0.8rem;
	    right: 0rem;
	    width: 60upx;
	    height: 60upx;
	}
	
	.content .out{
		width: 100%;
		height: 3rem;
		line-height: 3rem;
		position: relative;
		text-align: center;
		margin-top: 1rem;
		background-color: #fff;
	}

/*底部导航*/
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
