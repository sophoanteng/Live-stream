<template>
  <!-- <view class="web-top-bar double" id="doc-hd">
		<view class="topbar">
			<view class="container clearfix">
				<view class="hd-logo">
					<text href="#" class="links"></text>
				</view>
				<view class="hd-nav">
					<view class="item"><text class="hd-nav-text " @click="openUrl('/pages/pc/index/index')" :class="currpage=='index'?'current':''">首页</text> </view>
					<view class="item"><text class="hd-nav-text" @click="toDownloadApp">APP</text></view>
				</view>
				<view class="hd-login">
					<view class="no-login" v-if="!loginUser">
						<text href="javascript:void(0);" class="tologin" @click="toLogin()">登录/注册</text>
						<i class="icon-level"></i>
						<i class="icon-more"></i>
					</view>
					<view class="already-login flex-r" v-else>
						<view class="link flex-r" >
							<view class="icon-avatar">
							<img class="bg-grey" :src="loginUser.avatar_thumb" alt="">
							</view>
							<span class="nickname">{{loginUser.user_nicename}}</span>
							
							<text href="javascript:void(0);" class="tologin mg-l-10" @click="toLoginOut">退出</text>
						</view>
						<i class="icon-level"></i>
						<i class="icon-more"></i>
						<div class="userinfo" v-if="isShowUserMoreBtn">
							<div class="userinfo_up">
							</div>
							<div class="userinfo_down">
								<div class="userinfo_name">
									 <div class="live">
										<a href="/48541">我的直播</a>
									</div>
									<div class="live">
										<a href="/home/Personal/index">个人中心</a>
									</div>									
									<div class="logout">
										【退出登录】
									</div>
								</div>
							</div>
						</div>
					</view>
					<view class="huajiaodou">
						<a></a> 
					</view> 
				</view>
			</view>
		</view>
		<view class="top_line"></view>
		<view v-if="showLogin">
			<pc-login @loginok="loginok" v-if="showLogin"></pc-login>
		</view>
	</view> -->
  <view class="header-container">
    <view class="mr-[20px]">
      <img src="../../static/logo.png" class="inline-block mr-[20px]" alt="" />
    </view>
    <view class="mr-[20px]">
      <pc-button @on-click="openUrl('/pages/pc/index/index')">Front Page</pc-button>
    </view>
    <view class="mr-[20px] relative">
      <pc-button @on-click="categoryClick">Category
        <svg ref="arrow" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 492 492"
          style="enable-background: new 0 0 491.996 491.996"
          class="transition-all duration-500 fill-white w-[10px] inline-block ml-[5px]" xml:space="preserve">
          <path
            d="m484 125-16-16a27 27 0 0 0-38 0L246 293 62 109c-5-6-12-8-19-8s-14 2-19 8L8 125a27 27 0 0 0 0 38l219 220c5 5 12 8 19 8s14-3 19-8l219-220a27 27 0 0 0 0-38z" />
        </svg>
      </pc-button>
      <ul class="popover__menu" ref="category">
		<li v-for="item in liveclass" :key="item.id" class="popover__menu-item"><button @click="openUrl(`/pages/pc/category/category?id=${item.id}&title=${item.name}`)">{{item.name}}</button></li>
      </ul>
    </view>
    <view class="ml-auto float-right">
      <pc-button @on-click="!loginUser ? toLogin(): toLoginOut()"><svg data-name="Layer 1"
          xmlns="http://www.w3.org/2000/svg" viewBox="0 0 30 122.9" class="fill-white w-[18px] h-[21px]">
          <path
            d="M15 0A15 15 0 1 1 0 15 15 15 0 0 1 15 0Zm0 93a15 15 0 1 1-15 15 15 15 0 0 1 15-15Zm0-47A15 15 0 1 1 0 61a15 15 0 0 1 15-15Z"
            style="fill-rule: evenodd" />
        </svg></pc-button>
    </view>
    <view v-if="showLogin">
      <pc-login @loginok="loginok" v-if="showLogin"></pc-login>
    </view>
  </view>
</template>

<script>
const app = getApp();
export default {
  components: {},
  name: "pc-topbar",
  data() {
    return {
      showLogin: false,
      loginUser: null,
      isShowUserMoreBtn: false,
	  liveclass: []
    };
  },
  // props: ["liveclass"],
  // props: {
  //   currpage: {
  //     type: String,
  //     default() {
  //       return "";
  //     },
  //   },
  //   isLogin: {
  //     type: [Number, String],
  //     default() {
  //       return 0;
  //     },
  //   },
  // },
  methods: {
    openUrl(url) {
		console.log('url',url)
      uni.$emit("close_socket");
      uni.navigateTo({
        url: url
      });
    },
    initLoginUser() {
      this.loginUser = uni.getStorageSync("loginuser");
    },
    toDownloadApp() {
      window.open("http://35.241.92.99");
    },
    toLogin() {
      this.toLoginOut();
      this.$emit("loginResult", {});
      this.showLogin = true;
      this.$forceUpdate();
      return;
      console.log("login");
      uni.redirectTo({
        url: "/pages/pc/login/login",
      });
    },
    toLoginOut() {
      uni.removeStorageSync("uid");
      uni.removeStorageSync("token");
      uni.removeStorageSync("loginuser");
      console.log(uni.getStorageSync("uid"));
      this.initLoginUser();
    },
    loginok() {
      console.log("topbar login");
      console.log(e);
    },
    showUserMoreBtn() {
      this.isShowUserMoreBtn = true;
    },
    categoryClick() {
      this.$refs.arrow.classList.toggle("active__category");
      this.$refs.category.classList.toggle("popover__menu__active");
    },
    getClassLive(classId){
				//推荐主播
				uni.request({
					url: app.globalData.site_url +'/appapi/?service=Home.getClassLive&liveclassid='+classId+'&p='+1,
					method: 'GET',
					dataType: 'json',
					success: res => {
						for (var i = 0; i < this.liveclass.length; i++) {
							if(this.liveclass[i].id >= classId){
								this.liveclass[i].list = res.data.data.info;
								break;
							}
						}
						//this.liveclassList[classId] = res.data.data.info;
						//console.log(this.liveclassList);
					},
					fail: () => {},
					complete: () => {}
				});
			},
  },
  created() {
    let that = this;
    uni.$on("loginok", (e) => {
      console.log(e);
      that.showLogin = false;
      that.loginUser = e.user;
      that.$forceUpdate();

      let pages = getCurrentPages();
      let currPage = pages[pages.length - 1].route;
      if (currPage == "pages/pc/login/login") {
        uni.redirectTo({
          url: "/pages/pc/index/index",
        });
      }
    });
    uni.$on("closeLogin", (e) => {
      console.log(e);
      that.showLogin = false;
    });
    uni.$on("showLoginBox", (e) => {
      that.toLoginOut();
      that.showLogin = true;
    });
    this.initLoginUser();
    if (this.isLogin == 1) {
      this.toLogin();
    }
	uni.request({
		url: app.globalData.site_url + '/appapi/?service=Home.getConfig',
		method: 'GET',
		data: {},
		success: res => {
			let liveclass = res.data.data.info[0].liveclass;
			res.data.data.info[0].liveclass;
			for (var i = 0; i < liveclass.length; i++) {
				liveclass[i].list = [];
			}
			this.liveclass = liveclass;
			let currLoadNum = 0;
			if(this.liveclass && this.liveclass.length > 0){
				
				for (var i = 0; i < this.liveclass.length; i++) {
					if(currLoadNum >= this.classLoadNum){
						break;
					}
					if(this.liveclass[i].id > 0 && this.liveclass[i].name == "女神"){
						this.getClassLive(this.liveclass[i].id);
						currLoadNum ++;
					}
				}
			}
		},
		fail: () => {},
		complete: () => {}
	});
  },
};
</script>

<style>
@import url("@/common/pc/common.css");

.web-top-bar {
  position: -webkit-sticky;
  position: -moz-sticky;
  position: -ms-sticky;
  position: sticky;
  background-color: #fff;
  top: 0;
  z-index: 20;
}

.header-container {
  box-shadow: 0px 3px 6px #00000029;
  background: #ffffff 0% 0% no-repeat padding-box;
  padding: 20px 40px;
  display: flex;
  position: fixed;
  width: 100%;
  z-index: 20;
  grid-area: header;
}

.active__category {
  transform: rotate(180deg);
}

.popover__menu {
  border-radius: 6px;
  list-style-type: none;
  margin: 8px 0;
  min-width: 100%;
  padding: 5px;
  position: absolute;
  top: 100%;
  left: 0;
  border: 2px solid #4042521A;
  background: #F1F1F1;
  transition-property: transform, opacity;
  transition-duration: 200ms;
  transition-timing-function: ease;
  transform: scale(0.9);
  transform-origin: top left;
  opacity: 0;
  visibility: hidden;
}

.popover__menu-item {
  white-space: nowrap;
}

.popover__menu-item button {
  width: 100%;
  text-align: left;
  background: transparent;
  padding: 12px 14px;
  color: #101223;
  letter-spacing: 0px;
  font: normal normal bold 14px/20px Helvetica;
}

.popover__menu-item button::after {
  border: none;
  border-radius: 6px;
}

.popover__menu-item button:hover {
  background: transparent linear-gradient(101deg, #9672FB 0%, #D772FB 100%) 0% 0% no-repeat padding-box;
}

.popover__menu__active {
  transform: scale(1);
  display: block;
  opacity: 1;
  visibility: visible;
}
</style>
