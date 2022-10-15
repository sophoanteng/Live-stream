<template>
	<view class="content">
		<view class="fill">
			<image src="../../static/login/icon_reg_phone.png"></image>
			<view class="phone"><text class="area">+86</text><input type="number"  v-bind:value="mobile"   @input="mobile_input"  placeholder="请输入您的手机号" class="srphone" /></view>
		</view>
		<view class="fill">
			<image src="../../static/login/icon_reg_code.png"></image>
			<view class="code"><input type="number" v-bind:value="code" @input="code_input"  placeholder="请输入验证码" class="sms_code" /></view>

			<view v-if="showText==true" class="getcode" @click="getCode">发送验证码</view>
			<view v-else class="getcode">{{second}}s重新发送</view>
		</view>
		<view class="fill">
			<image src="../../static/login/icon_reg_pwd.png"></image>
			<view class="pwd"><input type="password" v-bind:value="pwd" @input="pwd_input" placeholder="请填写密码"/></view>
		</view>
		<view class="fill">
			<image src="../../static/login/icon_reg_pwd.png"></image>
			<view class="pwd"><input type="password" v-bind:value="pwds" @input="pwds_input"  placeholder="请填写确认密码"/></view>
		</view>
		<view class="msg">*短信验证码保障账户安全的同时短信费用将由平台支付</view>
		<button type="default" class="logon" @click="reg">注册并登录</button>
	</view>
</template>

<script>
	const app = getApp();
	
	export default {
		data() {
			return {
				mobile:'',
				pwd:'',
				pwds:'',
				code:'',
				second:60,
				showText:true,
			}
		},
		methods: {
			mobile_input(e){
				//手机号
				this.mobile=e.detail.value;
			},
			code_input(e){
				//验证码
				this.code=e.detail.value;
			},
			pwd_input(e){
				//密码
				this.pwd=e.detail.value;
			},
			pwds_input(e){
				//确认密码
				this.pwds=e.detail.value;
			},
			getCode(e){//倒计时
				if(this.mobile==''){
					uni.showToast({
						title: '请输入您的手机号',
						icon:'none',
						duration: 2000
					});
					return 1;
				}
				uni.request({ 
					url: app.globalData.site_url +'/appapi/?service=Login.getCode',
					method: 'GET',
					data: {
						country_code:'86',
						mobile:this.mobile,
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
						
						this.showText = false
						var interval = setInterval(() => {
						    let times = --this.second
						    this.second = times<10?'0'+times:times //小于10秒补 0
						}, 1000)
						setTimeout(() => {
						    clearInterval(interval)
						    this.second=60
						    this.showText = true
						}, 60000)
						
					},
					fail: () => {},
					complete: () => {}
				});
			}, 
			reg(e){  //注册
				if(this.mobile==''){
					uni.showToast({
						title: '请输入您的手机号',
						icon:'none',
						duration: 2000
					});
					return 1;
				}
				if(this.code==''){
					uni.showToast({
						title: '请输入验证码',
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
				if(this.pwds==''){
					uni.showToast({
						title: '请输入确认密码',
						icon:'none',
						duration: 2000
					});
					return 1;
				}
				
				if(this.pwd!=this.pwds){
					uni.showToast({
						title: '两次输入密码不一致',
						icon:'none',
						duration: 2000
					});
					return 1;
				}
				
				uni.request({
					url: app.globalData.site_url +'/appapi/?service=Login.userReg',
					method: 'GET',
					data: {
						country_code:'86',
						user_login:this.mobile,
						user_pass:this.pwd,
						user_pass2:this.pwds,
						code:this.code,
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
						
						//登入
						uni.request({
							url: app.globalData.site_url +'/appapi/?service=Login.userLogin',
							method: 'GET',
							data: {
								country_code:'86',
								user_login:this.mobile,
								user_pass:this.pwd,
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
								}else{
									uni.setStorageSync('uid',data.info[0]['id']);
									uni.setStorageSync('token',data.info[0]['token']);
									
									//跳转到首页
									uni.navigateTo({
										url:'../index/index',
									});
								}
								
								
							},
							fail: () => {},
							complete: () => {}
						});
				
					},
					fail: () => {},
					complete: () => {}
				});
				
				
				
			}
		}
	}
</script>

<style>
.content{
	width: 90%;
	margin: 0 auto;
}
.content .fill{
	width: 100%;
	height: 4rem;
	line-height: 4rem;
	border-bottom:1upx solid #999999;
	position: relative;
}
.content .fill image{
	width: 2rem;
	height: 2rem;
	margin-top: 1rem;
}

.content .fill .phone .area{
	position: absolute;
	top:-0.7rem;
	color: #000;
	font-size: 1rem;
	color:#999999;
	
}
.content .fill .phone .srphone{
	margin-left: 3rem
}
.content .fill .phone,.code,.pwd{
	position: absolute;
    top: 1rem;
    left: 3rem;
    
}
.content .fill .getcode{
	position: absolute;
	top: 0;
	right: 1rem;
	font-size: 1rem;
	color:#FA5A3A;
}
.content .fill view input{
	width: 80%;
	height: 2.5rem;
	line-height: 2.5rem;
	color: #000;
	font-size: 1rem;
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
.content .msg{
	width: 100%;
	height: 2.5rem;
	line-height: 2.5rem;
	font-size: 0.6rem;
	color: #999999;
}
</style>
