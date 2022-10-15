<template>
	<view class="width-full">
		<pc-topbar :isLogin="1"></pc-topbar>
		
	</view>
	
</template>
<style>
	.login-content{
		width: 600px;
		background-position: center;
		background-color: #fff;
		margin: 30px auto;
		position: relative;
	}
	.login-content .text-area{
		width: 100%;
		padding-top: 20px;
		padding-bottom: 20px;
		text-align: center;
	}
	.login-content .text-area .title{
		color: #000;
		letter-spacing:8upx;
		font-size: 30upx;
	}
	.login-content .login_in{
		width: 100%;
	}
	.login-content .login_in .phone{
		width: 70%;
		padding: 0 5%;
		height: 40px;
		line-height: 40px;
		background-color: rgb(255 255 255 / 55%);
		border-radius: 10px;
		position: relative;
		margin: 0 auto;
		color: #000;
		font-size: 16px;
	}
	.login-content .login_in .phone>input{
		position: absolute;
		top: 11.5%;
		height: 40px;
		line-height: 40px;
	}
	.uni-input-placeholder{
		color: #aaa;
	}
	.login-content .login_in .pwd{
		width: 70%;
		padding: 0 5%;
		height: 32px;
		line-height: 32px;
		background-color: rgb(255 255 255 / 55%);
		border-radius: 40px;
		margin: 0 auto;
		color: #aaa;
		font-size: 20px;
		margin-top: 20px;
	}
	
	.login-content .login_in .pwd>input{
		position: relative;
		top: 11.5%;
		height: 40px;
		line-height: 40px;
	}
	
	.content .logon{
		width: 80%;
		height: 50px;
		line-height: 50px;
		border-radius:40px;
		background-color: #FA5A3A;
		color: #000;
		margin-top: 30px;
	}
	.login-content .operation{
		width: 80%;
		height: 50px;
		line-height: 50px;
		border-radius: 40px;
		font-size: 16px;
		color: #aaa;
		margin: 0 auto;
		position: relative;
	}
	.login-content .operation .reg{
		width: 50%;
		float: left;
	}
	.login-content .operation .forget{
		width: 50%;
		text-align: right;
		float: right;
	}
	.login-content .treaty{
		width:100%;
		position: absolute;
		bottom:5%;
		font-size: 16px;
		color: #aaa;
		text-align: center
	}
	
</style>

<script>
	const app = getApp();
	console.log(app);
	export default {
		data() {
			return {
				mobile:'',
				pwd:'',
				windowHeight:0,
			}
		},
		onLoad() {
			this.windowHeight=uni.getSystemInfoSync().windowHeight;
		},
		methods: {
			mobile_input(e){
				//手机号
				this.mobile=e.detail.value;
			},
			pwd_input(e){
				//手机号
				this.pwd=e.detail.value;
			},
			logo(e){
				// 登入
				if(this.mobile==''){
					uni.showToast({
						title: '请输入您的手机号',
						icon:'none',
						duration: 2000
					});
					return 1;
				}
				if(this.pwd==''){
					uni.showToast({
						title: '请输入密码',
						icon:'none',
						duration: 2000
					});
					return 1;
				}
				
				uni.request({
					url: app.globalData.site_url +'/appapi/?service=Login.userLogin',
					method: 'GET',
					data: {
						country_code:'86',
						user_login:this.mobile,
						user_pass:this.pwd
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
						}else{
							
							uni.setStorageSync('uid',data.info[0]['id']);
							uni.setStorageSync('token',data.info[0]['token']);
							
							//跳转到首页
							uni.navigateTo({
								url:'../index/index',
							});
						}
						
						
					},
					fail: (data) => {
						console.log(data);
					},
					complete: () => {}
				});
				
			}
		}
	}
</script>


