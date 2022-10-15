<template>
	<view class="content" :style="'background-image: url(../../static/login/bg_login.png);height:'+this.windowHeight+'px'" >
		
		<view class="text-area">
			<text class="title">注册登录后体检更多精彩</text>
		</view>
		<view class="login_in">
			<view class="phone">+86<input type="number" v-bind:value="mobile" placeholder="请输入您的手机号" @input="mobile_input"  /></view>
			<view class="pwd"><input type="password" v-bind:value="pwd"  placeholder="请输入密码" @input="pwd_input" /></view>
		</view>
		<button type="default" class="logon" @click="logo">立即登录</button>
		<view class="operation">
			<view class="reg"><navigator url="../login/register">立即注册</navigator></view>
			<view class="forget"><navigator url="../login/forget">忘记密码</navigator></view>
		</view>
		<view class="treaty">登录即代表《隐私政策》和《服务协议》</view>
	</view>
</template>
<style>
	.content{
		width: 100vw;
		background-position: center;
	}
	.content .text-area{
		width: 100%;
		padding-top: 4rem;
		padding-bottom: 2rem;
		text-align: center;
	}
	.content .text-area .title{
		color: #FFFFFF;
		letter-spacing:8upx;
		font-size: 1.5rem;
	}
	.content .login_in{
		width: 100%;
	}
	.content .login_in .phone{
		width: 70%;
		padding: 0 5%;
		height: 2.6rem;
		line-height: 2.6rem;
		background-color: rgb(255 255 255 / 55%);
		border-radius: 2rem;
		position: relative;
		margin: 0 auto;
		color: #EEEEEE;
		font-size: 1rem;
	}
	.content .login_in .phone>input{
		position: absolute;
		top: 11.5%;
		left: 4rem;
		height: 2rem;
		line-height: 2rem;
	}
	.uni-input-placeholder{
		color: #EEEEEE;
	}
	.content .login_in .pwd{
		width: 70%;
		padding: 0 5%;
		height: 2.6rem;
		line-height: 2.6rem;
		background-color: rgb(255 255 255 / 55%);
		border-radius: 2rem;
		margin: 0 auto;
		color: #EEEEEE;
		font-size: 1rem;
		margin-top: 1rem;
	}
	
	.content .login_in .pwd>input{
		position: relative;
		top: 11.5%;
		height: 2rem;
		line-height: 2rem;
	}
	
	.content .logon{
		width: 80%;
		height: 2.5rem;
		line-height: 2.5rem;
		border-radius: 2rem;
		background-color: #FA5A3A;
		color: #fff;
		margin-top: 1.5rem;
	}
	.content .operation{
		width: 80%;
		height: 2.5rem;
		line-height: 2.5rem;
		border-radius: 2rem;
		font-size: 0.8rem;
		color: #EEEEEE;
		margin: 0 auto;
		position: relative;
	}
	.content .operation .reg{
		width: 50%;
		float: left;
	}
	.content .operation .forget{
		width: 50%;
		text-align: right;
		float: right;
	}
	.content .treaty{
		width:100%;
		position: absolute;
		bottom:5%;
		font-size: 0.8rem;
		color: #EEEEEE;
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


