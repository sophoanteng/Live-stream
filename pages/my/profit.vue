<template>
	<view class="content">
		<image class="img-bg" src="../../static/my/bg_profit_1.png"></image>
		<view class="v-float">
			<view class="text-area">
				<view class="v-top">
					<text class="tv-gray">总映票数</text>
					<text class="tv-black">{{votestotal}}</text>
				</view>
				<view class="line"></view>
				<view class="v-bottom">
					<text class="tv-gray">可提取映票数</text>
					<text class="tv-yellow">{{votes}}</text>
				</view>
				
			</view>
		</view>
	</view>
</template>

<script>
	const app = getApp();
	export default {
		data() {
			return {
				uid: '',
				token: '',
				votes:0,
				votestotal:0
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
				url: app.globalData.site_url +'/appapi/?service=User.getProfit',
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
					this.votes=data['info'][0]['votes'];
					this.votestotal=data['info'][0]['votestotal'];
										
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
		height: 800rpx;
	}
	.img-bg {
		height: 300rpx;
		width: 100%;
		position: absolute;
	}
	.v-float{
		position: absolute;
		width: 100%;
		height: auto;
		margin-top: 50rpx;
		display: flex;
		flex-direction: column; 
		align-items: center;
		/* background-color: #DD524D; */
	}

	.text-area {
		width: 580rpx;
		height: 380rpx;
		margin-top: 50rpx;
		background-image: url(../../static/my/bg_profit_2.png) ;
		background-repeat: no-repeat;
		background-size: 100%;
		display: flex;
		flex-direction: column;
		padding-left: 20rpx;
		padding-right: 20rpx;
		
	}
	
	
	.v-top{
		height: 190rpx;
		display: flex;
		flex-direction: column;
		justify-content: center ;
		padding-left: 20rpx;
		padding-right: 20rpx;
	}
	
	.v-bottom{
		height: 190rpx;
		display: flex;
		flex-direction: row;
		padding-left: 20rpx;
		padding-right: 20rpx;
		align-items: center;
	}
	
	.line{
		height: 2rpx;
		width: 100%;
		background-color: #f7f7f7;
	}

	.title {
		font-size: 36rpx;
		color: #ffffff;
	}
	
	.tv-black{
		color: #000000;
		font-weight: bold;
	}
	
	.tv-gray{
		color: #808080;
	}
	
	.tv-yellow{
		color: #f36435;
		margin-left: 40rpx;
	}
	
</style>
