<template>
	<view class="container flex">

		<view @click="photo" class="avatar-wrap">
			<image class="user-avatar" :src="avatar" mode="aspectFill"></image>
			<text class="tishi">点击更换头像</text>
		</view>
		<view class="nickname"><input type="text"  v-bind:value="user_nicename"  placeholder="请输入昵称" @input="nickname_input" /></view>
		
		<button type="default" class="deposit">保存</button>
	</view>
</template>

<script>
	import qiniuUploader from '../../qiniuUploader.js';
	const app = getApp();
	export default {
		data() {
			return {
				uid:'',
				token:'',
				avatar:'',
				user_nicename:'',
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
					console.log(data);
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
			// 上传图片
			photo() {
				let gData = app.globalData;
				let that = this;
				uni.chooseImage({
					count: 1, //默认9
					sizeType: ['original', 'compressed'], //可以指定是原图还是压缩图，默认二者都有
					sourceType: ['album', 'camera'], //从相机/相册选择
					success: function(res) {
						//上传图片到服务器并返回图片地址(这里不用七牛)
						let tempFilePaths = res.tempFilePaths;
						let that2 = that;
						var path = tempFilePaths[0];
						uni.request({
							url: app.globalData.site_url +'/appapi/?service=Upload.getCosinfo',
							method: 'POST',
							data: {
								'uid': app.globalData.userinfo.id,
								'token': app.globalData.userinfo.token
							},
							success: res2 => {
								uni.hideLoading();
								if (res2.data.data.code == 0) {
									var QiNiutoken = that.decypt(res2.data.data.info[0]['qiniuInfo']['token']);
									var name = 'UNIAPPicon_edu_kaiyuan' + that.getTime() + 'icon.png';
									qiniuUploader.upload(path, res => {
										uni.showToast({
											title: '上传成功',
											icon: 'none'
										});
										that.avatarPath = res.imageURL;
										that.avatar_name = name;
									}, error => {
										uni.showToast({
											title: '上传失败，请重试',
											icon: 'none'
										});
										
									}, {
										region: 'ECN',
										domain: app.globalData.qiniuimageurl,
										key: name,
										uptoken: QiNiutoken,
									});
								}
							},
							fail() {
								uni.hideLoading();
								uni.showToast({
									title: '提交失败',
									icon: 'none'
								});
							},
						});
					},
				});
			},
		}
	}
</script>

<style>
.container{
    width: 100%;
    height: 100%;
}

.avatar-container{
    position: relative;
    text-align: center;
    padding-top: 50%;
}

.avatar-wrap {
	width: 180rpx;
	height: 200rpx;
	margin: 80rpx auto;
	text-align: center;
}

.user-avatar {
	width: 150rpx;
	height: 150rpx;
	border-radius: 50%;
}

.tishi {
	display: block;
	font-size: 18rpx;
	color: #969696;
}


.nickname{
	width: 90%;
	height: 2rem;
	line-height: 2rem;
	text-align: center;
	border-bottom: 1px solid #eee;
	margin: 0 auto;
}
.nickname input{
	font-size: 0.9rem;
}

.deposit{
	width: 50%;
	height: 2.5rem;
	line-height: 2.5rem;
	border-radius: 2rem;
	background-color: #FA5A3A;
	color: #fff;
	margin-top: 2.5rem;
}
</style>
