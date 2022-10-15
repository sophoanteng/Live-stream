<template>
	<view>
		<pc-topbar/>
		<view class="grid-container">
			<video-carousel :videos="videos"/>
			<!-- <pc-carousel :recommendLists="recommendLists" /> -->
			<pc-group-thumbnail :items="items" title="Hot Channel"/>
			<watch-channel-thumbnail :watchItem="items" title="Watched Channel"/>
			<sexy-channel-thumbnail :classLiveList="items" title="Sexy Channel"/>
			<cosplay-channel-thumbnail :dataClassList="items" title="Cosplay Channel"/>
			<dancing-channel-thumbnail :dancingList="items" title="Dancing Channel"/>
			<pc-sidebar />
		</view>
	</view>
</template>
<script>
	const app = getApp();
	let videoIds = ["sG3BomZqem4", "V0TyHhXkjsE", "nUVRTWWb-8I", "6xz_dbmYc1g", "s3fdI6dw1Tk"];
	export default {
		data() {
			return {
				lists: [],
				items: [],
				watchItem: [],
				classLiveList: [],
				recommendLists: [],
				dancingList: [],
				listHeight: 500,
				liveclass: [],
				dataClassList: [],
				liveclassList:{},
				classLoadNum: 4,
				profitList:[],
				consumeList:[],
				currentVideo: 0,
        // totalVideos: 0,
        videos: [],
        videoPositions: {
          0 : "tertiary tertiary-left",
          1 : "secondary secondary-left",
          2 : "main",
          3 : "secondary secondary-right",
          4 : "tertiary tertiary-right"
        }
			}
		},
	async onLoad(e) {
			await this.getPageData();
			await this.getClassLive();
			let sysInfo = uni.getSystemInfoSync()
			this.windowWidth = sysInfo.screenWidth
			this.windowHeight = sysInfo.windowHeight - 80
			this.listHeight = sysInfo.windowHeight - 80 - 50;
			
		},
		methods: {
			toListPage(title,type,classId){
				uni.redirectTo({
					url:"/pages/pc/list/list?title="+title+"&type="+type+"&class_id="+classId
				})
			},
			toLogin() {
				uni.navigateTo({
					url: "/pages/pc/login/login"
				})
			},
			loginResult() {
				// console.log("loginResult");
			},
			onClassClick(item) {
				uni.navigateTo({
					url: '/pages/pc/class/class?id=' + item.id + "&title=" + item.name,
				});
			},
			async getPageData() {
				uni.request({
				url: app.globalData.site_url +'/appapi/?service=Home.getClassLive',
				method: 'GET',
				data: {
					liveclassid: 7,
					p: 1,
				},
				success: res => {
					let liveclass = res.data.data;
					this.dancingList = liveclass
					console.log('liveclassListttttgg data',this.dancingList)
				},
				fail: () => {},
				complete: () => {}
			});
				uni.request({
				url: app.globalData.site_url +'/appapi/?service=Home.getClassLive',
				method: 'GET',
				data: {
					liveclassid: 9,
					p: 1,
				},
				success: res => {
					let liveclass = res.data.data;
					this.dataClassList = liveclass
				},
				fail: () => {},
				complete: () => {}
			});
				uni.request({
				url: app.globalData.site_url +'/appapi/?service=Home.getClassLive',
				method: 'GET',
				data: {
					liveclassid: 8,
					p: 1,
				},
				success: res => {
					let liveclass = res.data.data;
					this.classLiveList = liveclass
					// console.log('liveclassListtttt',this.classLiveList)
				},
				fail: () => {},
				complete: () => {}
			});
						//推荐主播
						await uni.request({
				  	url: app.globalData.site_url + '/appapi/?service=Home.GetRecommendLive',
					method: 'GET',
					data: {
						p:1
					},
					success: res => {
						this.recommendLists = res.data.data.info;
						for (let i=0; i<this.recommendLists.length - (this.recommendLists.length - 5); i++) {
							let videoId = videoIds[i];
							let videoEmbed = this.recommendLists[i].pull
							let videoImage = "https://img.youtube.com/vi/" + videoId + "/maxresdefault.jpg";
							let channelImage = "https://yt3.ggpht.com/a-/AAuE7mCXgsNVSlp9-BLuKFoaAqKxpjJ0T1NB1yJzdQ=s288-mo-c-c0xffffffff-rj-k-no";
							let video = {
								thumbnail: videoImage,
								embed: videoEmbed,
								position: this.videoPositions[i],
								videoId: videoId,
								active: false,
								channelImage: channelImage,
								channelName: "",
								channelTitle: "",
								channelViews: 2345,
								uid: this.recommendLists[i].uid,
								stream: this.recommendLists[i].stream,
								anyway: this.recommendLists[i].anyway,
								user_nicename: this.recommendLists[i].user_nicename,
								anyway: this.recommendLists[i].anyway,
								avatar_thumb: this.recommendLists[i].avatar_thumb,
								pull: this.recommendLists[i].pull,
							}
							
							this.videos.push(video);
						}
					},
					fail: () => {},
					complete: () => {}
				});
				await	uni.request({
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
				// 热门主播
				await	uni.request({
					url: app.globalData.site_url + '/appapi/?service=Home.gethot',
					method: 'GET',
					data: {
						p:1,
						// pnum: 3
					},
					success: res => {
						this.slide = res.data.data.info[0].slide;
						this.items = res.data.data.info[0].list;
					},
					fail: () => {},
					complete: () => {}
				});
				await	uni.request({
					url: app.globalData.site_url + '/appapi/?service=Live.getWatchedChannelList',
					method: 'GET',
					data: {
						uid : 45639,
						p: 1,
						token: 'f7be04f77b376cd032e648bef5b608f3'
					},
					success: res => {
						this.slide = res.data.data.info[0].slide;
						this.watchItem = res.data.data.info[0];
					},
					fail: () => {},
					complete: () => {}
				});
				
				this.getConsumeList();
				this.getProfitList();
			},
		async getClassLive(classId){
				//推荐主播
			await uni.request({
					url: app.globalData.site_url +'/appapi/?service=Home.getClassLive&liveclassid='+classId+'&p='+1,
					method: 'GET',
					dataType: 'json',
					success: res => {
						// console.log(res);
						for (var i = 0; i < this.liveclass.length; i++) {
							if(this.liveclass[i].id >= classId){
								this.liveclass[i].list = res.data.data.info;
								break;
							}
						}
						this.liveclassList[classId] = res.data.data.info;
					},
					fail: () => {},
					complete: () => {}
				});
			},
			//消费榜单
			getConsumeList(){
				//推荐主播
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
			},
			//消费榜单
			getProfitList(){
				//推荐主播
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
			},
		},
	}
</script>

<style>
	@import url("@/common/pc/common.css");
	@import url("@/common/pc/index.css");
	@import url("@/common/pc/rebo-common.css");
	@import url("@/common/pc/rebo-index.css");

	.page-content {
		display: flex;
		max-width: 2000px;
	}
	
	.grid-container {
		position: relative;
		width: calc(100vw - 350px);
		margin-right: 350px;
		padding-top: 92px;
		padding-bottom: 3rem;
		display: flex;
		justify-content: center;
		align-content: flex-start;
		flex-wrap: wrap;
	}
 	
</style>
