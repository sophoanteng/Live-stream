<template>
	<view>
		<view class="login_pop_cover js_login_pop_cover" style="display: block;"></view>
		<div class="login_pop js_login_pop" style="display: block;">
			<div class="title">
				<span class="close-btn cuIcon-close" @click="closeLogin"></span>
				<block v-if="optionType == 1">登 录</block> 
				<block v-if="optionType == 2">注 册</block> 
				<block v-if="optionType == 3">找回密码</block> 
			</div>
		
			<article>
				<div class="tips"></div>
			</article>											
			<article>
				<div class="phoneArea flex-r flex-y-center">													
					<i class="iconfont cuIcon-mobile f-16 phoneIcon"></i>													
					<input class="phone  js_login_phone_input" type="text" placeholder="输入手机号码" maxlength="11" v-model="mobile" >	
				</div>
				<div class="key_con" v-if="optionType == 2 || optionType == 3">
					<div class="keyBorder flex-r flex-y-center">														
					<i class="phoneIcon cuIcon-safe"></i>														
					<input class="key js_reg_code_input" type="text" placeholder="输入验证码" maxlength="6">													
					</div>													
					<a class="get_none js_reg_getcode" @click="getCode" v-if="showText">获取验证码</a>
					<a class="get_none js_reg_getcode" v-else>{{second}}s重新发送</a>
				</div>
				<div class="phoneArea">													
					<i class="phoneIcon cuIcon-lock "></i>
					<input class="pass js_login_pass_input" type="password" placeholder="输入密码" v-model="pwd">
				</div>
				<div class="phoneArea" v-if="optionType == 2 || optionType == 3">
					<i class="phoneIcon cuIcon-lock "></i>
					<input class="pass js_login_pass_input" type="password" placeholder="再次输入密码" v-model="pwds">
				</div>
				<p>
					<block v-if="optionType == 1">
						<span class="login-btn fl js-reg" @click="setOptionType(2)">注册</span>
						<span class="login-btn fr js-forget" @click="setOptionType(3)">忘记密码</span>
					</block>
					<block v-if="optionType == 2">
						<span class="login-btn fl js-reg" @click="setOptionType(1)">登录</span>
						<span class="login-btn fr js-forget" @click="setOptionType(3)">忘记密码</span>
					</block>
					<block v-if="optionType == 3">
						<span class="login-btn fl js-reg" @click="setOptionType(2)">注册</span>
						<span class="login-btn fr js-forget" @click="setOptionType(1)">登录</span>
					</block>
				</p>
				<p></p>
				<!-- <a class="submit js_login_submit get_none">确认</a> -->
				<a class="submit js_login_submit get_key" @click="login" v-if="optionType == 1">登录</a>
				<a class="submit js_login_submit get_key" @click="reg" v-if="optionType == 2">注册并登录</a>
				<a class="submit js_login_submit get_key" @click="reg" v-if="optionType == 3">立即找回</a>
			</article>
			<p class="other_login_tip"></p>	
		</div>
	</view>
</template>

<script>
	const app = getApp();
	export default {
		name:"pc-login",
		data() {
			return {
				mobile:'',
				pwd:'',
				windowHeight:0,
				code:"",
				pwds:"",
				second:60,
				showText:true,
				optionType:1,//1登录 2注册 3找回密码
			};
		},
		methods:{
			mobile_input(e){
				//手机号
				this.mobile=e.detail.value;
			},
			pwd_input(e){
				//手机号
				this.pwd=e.detail.value;
			},
			setOptionType(type){
				this.optionType = type;
			},
			closeLogin(){
				uni.$emit("closeLogin",{});
			},
			login(e){
				let that = this;
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
							uni.setStorageSync('loginuser',data.info[0]);
							console.log("loginok");
							uni.$emit("loginok",{user:data.info[0]});
						}
					},
					fail: (data) => {
						console.log(data);
					},
					complete: () => {}
				});
				
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
				let apiUrl = "";
				if(this.optionType == 2){
					apiUrl = app.globalData.site_url +'/appapi/?service=Login.getCode';
				}else{
					apiUrl = app.globalData.site_url +'/appapi/?service=Login.getForgetCode';
				}
				uni.request({ 
					url: apiUrl,
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
				var that = this;
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
						that.login(e);
					},
					fail: () => {},
					complete: () => {}
				});
			},
			forget(e){  //找回密码
				var that = this;
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
					url: app.globalData.site_url +'/appapi/?service=Login.userFindPass',
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
						
							uni.showToast({
								title: data.msg,
								icon:'none',
								duration: 2000
							});
						if(data.code==0){
							//延迟跳转
							//登入
							that.login(e);
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
.close-btn{
	position: absolute;
	display: block;
	right: 15px;
	height: 16px;
	width: 15px;
	background-position: 0 -242px;
	opacity: .9;
	-webkit-transition: all 0s;
	transition: all 0s;
	cursor: pointer;
}

.login_pop a {
	text-decoration: none
}

.login_pop .login_popbox .close {
	position: absolute;
	display: block;
	right: 4px;
	top: 30px;
	height: 16px;
	width: 15px;
	background-position: 0 -242px;
	opacity: .6;
	-webkit-transition: all 0s;
	transition: all 0s
}

.login_pop .login_popbox .close:hover {
	-webkit-transform: none;
	transform: none;
	opacity: 1;
	-webkit-transition: all 0s;
	transition: all 0s
}

.login_pop_cover {
	width: 100%;
	height: 2000px;
	background-color: rgba(0,0,0,0.5);
	filter: progid:DXImageTransform.Microsoft.gradient(startColorstr=#80000000,endColorstr=#80000000);
	position: fixed;
	_position: absolute;
	left: 0;
	top: 0;
	z-index: 200;
	display: none
}

.login_pop {
	width: 363px;
	background: white;
	position: fixed;
	_position: absolute;
	z-index: 200;
	top: 10%;
	left: 50%;
	margin-left: -180px;
	border-radius: 5px;
	overflow: visible;
	display: none
}

.login_pop .title {
	width: 100%;
	height: 55px;
	line-height: 53px;
	font-size: 24px;
	color: white;
	text-align: center;
	position: relative;
	background: #d14c49
}

.phoneArea {
	width: 245px;
	margin: 0 auto;
	margin-top: 22px;
	border: 1px solid #b0b0b0;
	border-radius: 5px;
	overflow: hidden
}

.phoneArea .phoneIcon {
	display: block;
	float: left;
	width: 40px;
	height: 33px;
	line-height: 33px;
	text-align: center;
	background-repeat: no-repeat;
	background-position: 9px -153px;
	_background-repeat: no-repeat;
	_background-position: 10px 0
}


.login_pop .close {
	width: 22px;
	height: 23px;
	background-position: 3px -239px;
	background-repeat: no-repeat;
	position: absolute;
	right: 14px;
	top: 16px;
	cursor: pointer;
	border-radius: 50%;
	-webkit-transform: rotate(0deg);
	transform: rotate(0deg);
	-webkit-transition: all 1s;
	transition: all 1s;
	_background-position: 0 0
}

.login_pop article {
	position: relative;
	overflow: hidden;
	min-height:30px;
}

.login_pop .close:hover {
	-webkit-transform: rotate(360deg);
	transform: rotate(360deg);
	-webkit-transition: all 1s;
	transition: all 1s
}

.login_pop article .key_con {
	width: 245px;
	margin: 0 auto;
	margin-top: 22px;
	border-radius: 5px;
	overflow: hidden
}
.login_pop article  .key_con  .phoneIcon {
	display: block;
	float: left;
	width: 40px;
	height: 33px;
	line-height: 33px;
	text-align: center;
	background-repeat: no-repeat;
	background-position: 9px -153px;
	_background-repeat: no-repeat;
	_background-position: 10px 0
}
.keyBorder {
	float: left;
	height: 33px;
	border: 1px solid #a0a0a0;
	overflow: hidden;
	border-radius: 5px
}

.keyIcon {
	display: block;
	float: left;
	width: 40px;
	height: 33px;
	background-repeat: no-repeat;
	background-position: 9px -199px;
	_background-repeat: no-repeat;
	_background-position: 10px 0
}

.login_pop article .phone,.login_pop article .pass,.login_pop article .key {
	color: grey;
	font-size: 12px;
	border: 0;
	height: 32px;
	line-height: 36px;
	padding: 0;
	margin: 0;
	-webkit-appearance: none;
	-moz-appearance: none;
	appearance: none;
	-webkit-tap-highlight-color: transparent;
	outline: 0;
	_float: left
}

.select_country{
	width: 83%;
	height: 33px;
	border: 0;
}

.login_pop article .phone {
	width: 205px
}
.login_pop article .pass {
	width: 205px
}
.login_pop article .key {
	width: 114px;
	float: left
}

.login_pop article .get_key {
	width: 80px;
	height: 32px;
	padding: 0;
	margin: 2px 1px 0 0;
	line-height: 32px;
	background: #d14c49;
	float: right;
	border: 0;
	text-align: center;
	color: #fff;
	font-size: 12px;
	cursor: pointer;
	border-radius: 16px
}

.login_pop article .get_key:hover {
	background-color: #d66260;
	color: #fff
}

.login_pop article .get_none {
	width: 80px;
	height: 32px;
	padding: 0;
	margin: 0 1px 0 0;
	line-height: 32px;
	background: #f5f5f5;
	float: right;
	border: 1px solid #eaeaea;
	text-align: center;
	color: #636363;
	font-size: 12px;
	cursor: default;
	border-radius: 20px
}

.login_pop article p {
	width: 100%;
	text-align: center;
	color: #7b7b7b;
	font-size: 12px;
	height: 22px;
	margin-top: 10px
}
.login_pop article p .login-btn{
	cursor:pointer;
}
.login_pop article p .fl{
	float:left;
	margin-left: 60px;
}
.login_pop article p .fr{
	float:right;
	margin-right: 60px;
}
.login_pop article p em {
	color: #da5537;
	font-style: normal
}

.login_pop article .submit {
	width: 218px;
	height: 38px;
	line-height: 38px;
	font-size: 20px;
	text-align: center;
	border: 0;
	color: white;
	display: block;
	margin: 0 auto;
	background-color: #d14c49
}

.login_pop article .submit:hover {
	background-position: 0 -42px;
	_background-position: 0 0
}

.login_pop article .submit.get_none {
	float: none;
	background-color: #f5f5f5;
	color: #989898
}

.login_pop article .submit.get_key {
	float: none;
	background-color: #d14c49;
	color: #fff
}

.login_pop .other_login_tip {
	width: 100%;
	height: 40px;
	background-repeat: no-repeat;
	background-position: -65px -390px
}

.login_pop .weibo,.login_pop .weixin,.login_pop .qq {
	background-repeat: no-repeat;
	background-size: auto 100%;
	width: 66px;
	height: 84px;
	float: left;
	margin: 34px 0;
	cursor: pointer
}

.login_pop .weibo {
	background-position: -200px 0;
	margin-left: 44px
}

.login_pop .weixin {
	background-position: 0 0;
	margin-left: 40px
}

.login_pop .qq {
	background-position: -100px 0;
	margin-left: 40px
}



.tips {
	width: 100%;
	height: 14px;
}

.warring {
	width: 100%;
	height: 15px;
	font-size: 12px;
	color: #d14c49;
	text-align: center;
	display: none;
	position: absolute;
	top: 3px
}

.login_popbox .warring {
	top: 144px;
	width: 139px;
	left: 23px
}

.login_popbox .get_none {
	background-color: #929292!important;
	color: #fff!important
}

.login_pop .login_popbox {
	position: fixed;
	width: 100%;
	height: 100%;
	top: 0;
	left: 0;
	background-color: rgba(0,0,0,0.5);
	display: none
}

.login_pop .login_popbox .inner {
	position: absolute;
	width: 265px;
	height: 236px;
	top: 50%;
	left: 50%;
	margin-left: -132px;
	margin-top: -128px;
	background-repeat: no-repeat;
	background-position: 0 0;
	background-size: 100% auto
}

.login_pop .login_popbox input {
	border: 0;
	position: absolute;
	top: 111px;
	left: 25px;
	width: 135px;
	height: 31px;
	background-color: #fff;
	text-indent: 10px
}

.login_pop .login_popbox img {
	position: absolute;
	top: 111px;
	right: 20px;
	background-color: #fff;
	display: block;
	width: 75px;
	height: 32px;
	border: 0;
	cursor: pointer
}

.login_pop .login_popbox a {
	display: block;
	position: absolute;
	bottom: 25px;
	background-color: #34aff3;
	width: 222px;
	height: 40px;
	border-radius: 20px;
	left: 50%;
	margin-left: -111px;
	text-align: center;
	color: #fff;
	line-height: 40px;
	font-size: 14px;
	cursor: pointer
}

</style>