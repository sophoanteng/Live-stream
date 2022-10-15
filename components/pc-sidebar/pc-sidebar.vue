<template>
		<view id="right" class="contain-space column">
			<view class="back-green">
				<h4 class="title-ban">Popular List's</h4>
				<img class="img-side" src="static/side-bar/side-banner.png" alt="">
				<view class="between-profile-view" v-for="(popular, index) in profitList" :key="index" >
					<view class="contain-profile">
						<img class="icons" src="static/side-bar/Icons/Rank-1.png" alt="">
						<img class="circle-profile" :src="popular.avatar" alt="">
						<view class="contain-name">
							<h4> {{popular.user_nicename}}</h4>
						</view>
					</view>
					<view class="contain-view">
						<img class="daimon" src="static/side-bar/Icons/Daimond.png" alt="">
						<p>{{popular.totalcoin}}</p>
					</view>
				</view>
				<h4 class="title-ban">Rich List's</h4>
				<img class="img-side" src="static/side-bar/side-banners.png" alt="">
				<view class="between-profile-view" v-for="(rich, index) in consumeList" :key="'a'+index">
					<view class="contain-profile">
						<img class="icons" src="static/side-bar/Icons/Rank-1.png" alt="">
						<img class="circle-profile" :src="rich.avatar" alt="">
						<view class="contain-name">
							<h4>{{rich.user_nicename}}</h4>
						</view>
					</view>
					<view class="contain-view">
						<img class="daimon" src="static/side-bar/Icons/Daimond.png" alt="">
						<p>{{rich.totalcoin}}</p>
					</view>
				</view>
			</view>
		</view>
  </template>
  
  <script>
  import pcAvatar from "../pc-avatar/pc-avatar.vue";
  const app = getApp();
  export default {
    components: { pcAvatar },
    name: "pc-sidebar",
	// props: ['profitList', 'consumeList'],
    data() {
      return {
		  profitList: [],
		  consumeList: []
	  };
    }, 
	created() {
		uni.request({
			url: app.globalData.site_url +'/appapi/?service=Home.ProfitList&type=total&uid=1'+1,
			method: 'GET',
			dataType: 'json',
			success: res => {
				this.profitList = res.data.data.info;
			},
			fail: () => {},
			complete: () => {}
		});
		
		uni.request({
			url: app.globalData.site_url +'/appapi/?service=Home.ConsumeList&type=total&uid='+1,
			method: 'GET',
			dataType: 'json',
			success: res => {
				this.consumeList = res.data.data.info;
			},
			fail: () => {},
			complete: () => {}
		});
	}
  };
  </script>
  
  <style>
	#right {
		margin-top: 3em;
		height: calc(100vh - 50px - 3.5rem);
		width: 15rem;
		overflow: scroll;
		background-color: #19171c;
		color: #d2c9e8;
		position: fixed;
		top: 50px;
		padding-bottom: 3.5rem;
		flex: 0 0 auto;
		right: 0;
		width: 350px;
		height: 1647px;
		background: white;
		overflow: scroll;
		max-height: 100vh;
	}
    .back-green {
		position: relative;
	}
	.back-green .title-ban {
		text-align: center;
		position: absolute;
		margin-left: auto;
		margin-right: auto;
		left: 0;
        right: 0;
		margin-top: 27px;
		color: white;
		font-size: 20px;
	}
	.text-back {
		background-color: hotpink;
		height: 70px;
		text-align: center;
		justify-content: center;
		display: flex;
		align-items: center;
        margin-bottom: 10px;
	}
	.text-back h4 {
		color: white;
	}
	.between-profile-view {
		display: flex;
		justify-content: space-between;
		padding: 4.5px 12px;
	}
	.contain-profile {
		display: flex;
		margin-top: 8px;
	}
	.icons {
		width: 20px;
		height: 20px;
		background-color: aqua;
		border-radius: 40px;
		margin-top: 5%;
	}
	.daimon {
		width: 20px;
		height: 20px;
		background-color: aqua;
		border-radius: 40px;
		margin-top: 5%;
	}
	.circle-profile {
		width: 30px;
		height: 30px;
		background-color: chocolate;
		border-radius: 40px;
		margin-left: 6px;
	}
	.contain-name {
		margin-left: 10px;
		margin-top: 5px;
	}
	.contain-name h4 {
		font-size: 12px;
        font-weight: bold;
	}
	.contain-name p {
		font-size: 11px;
		color: gray;
	}
	.contain-view {
	display: flex;
    margin-top: 10px;
    align-items: self-end;
    width: 50px;
    justify-content: start;
	}
	.contain-view p {
		font-size: 11px;
		font-weight: 600;
		margin-top: 4px; 
		padding: 2px;
		margin-right: -7px;
        margin-left: 2px;
	}
  </style>
  