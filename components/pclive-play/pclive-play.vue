<template>
	<view class="uni-video-container" id="myPlayer" ref="video">
		
		<block if="currItem">
			<a class="common-text-f16 common-color-fff common-block common-text-center common-absolute goto-video"
				@click="toRoom">进入直播间</a>
			<div class="common-absolute common-text-f14 common-color-fff-gray room-num">主播：<span
					id="video-play-haoma">{{item.user_nicename}}</span></div>
		</block>
	</view>
</template>

<script>
	export default {
		name:"pclive-play",
		data() {
			return {
				flvPlayer: null,
				player: null,
				autoplay:true,
				myVideo:null,
				videoInterval:0
			};
		},
		props:{
			item:{
				type:Object,
				default(){
					return {};
				}
			},
			boxHeight:{
				type:[Number,String],
				default(){
					return "400";
				}
			},
			boxWidth:{
				type:[Number,String],
				default(){
					return "500";
				}
			}
		},
		methods:{
			toRoom(){
				//判断是否登入
				var res = global.isLogin();
				if(!res){
					uni.showModal({
						title:'请登录',
						content:"请登录",
						showCancel:false,
						success:function(){
							uni.$emit("showLoginBox",{});
						}
					})
					return;
				}
				console.log("onItemClick");
				uni.navigateTo({
					url:'/pages/pc/livepull/livepull?liveuid='+this.item.uid+'&stream='+this.item.stream+'&anyway='+this.item.anyway+'&live_nicename='+this.item.user_nicename+'&live_avatar='+this.item.avatar_thumb,
				});
			},
			getLivePlayer() {  // 生成需要的video 组件
				if(this.item.pull.indexOf('.flv') == -1){
					let oldvideo = document.getElementById("live_video");
					if(oldvideo){
						oldvideo.remove();
					}
					var video = document.createElement('video');
					video.setAttribute('x5-video-player-type', 'h5') //安卓 声明启用同层H5播放器 可以在video上面加东西
					video.id = 'live_video';
					video.className = "video-js";
					video.style = 'width:' + this.boxWidth + 'px;height: '+ this.boxHeight + 'px';
					//video.controls = true;
					video.setAttribute("muted","muted");
					var source = document.createElement('source');
					source.src = this.item.pull;
					//根据流地址的后缀设置好播放类型
					if (source.src.indexOf('.mp4') !== -1) {
					    //mp4类型
					    source.type = 'video/mp4'
					} else if (source.src.indexOf('.m3u8') !== -1) {
					    //hls类型
					    source.type = 'application/x-mpegURL'
					} else if (source.src.indexOf('.flv') !== -1) {
					    //flv类型
					    source.type = 'video/flv'
					} else {
					    //rtmp类型
					    source.type = 'rtmp/hls'
					}
					video.appendChild(source);
					//this.$refs.video.$el.appendChild(video);
					document.getElementById("myPlayer").appendChild(video);
					this.myVideo = videojs('live_video',{ 
						autoplay: true,
						controls: true,
						techOrder: ['html5', 'flash'], //播放顺序
						loop: true, // 导致视频一结束就重新开始。 视频播放结束后，是否循环播放
						controlBar:{
							children: [
							  {name: 'playToggle'}, // 播放按钮
							  {name: 'currentTimeDisplay'}, // 当前已播放时间
							  //{name: 'progressControl'}, // 播放进度条
							  {name: 'durationDisplay'}, // 总时间
							  { // 倍数播放
								name: 'playbackRateMenuButton',
								'playbackRates': [0.5, 1, 1.5, 2, 2.5]
							  },
							  {
								name: 'volumePanel', // 音量控制
								inline: false, // 不使用水平方式
							  },
							  {name: 'FullscreenToggle'} // 全屏
							]
						}
					});
					video.play();
					return;
				}
				
				//return;
				// var player = document.getElementById('videoElement');
				
				var player = document.createElement('video')
				player.id = 'video'
				//player.className='pull'
				player.style = 'width:' + this.boxWidth + 'px;height: '+ this.boxHeight + 'px'
				player.enableProgressGesture = this.enableProgressGesture
				player.controls=this.controls
				player.showCenterPlayBtn=this.showCenterPlayBtn
				player.showPlayBtn=this.showPlayBtn
				player.setAttribute("muted","muted");
				player.showFullscreenBtn=this.showFullscreenBtn
				player.x5VideoPlayerType='h5-page'
				player.x5VideoPlayerFullscreen="false" 
				//player.src="http://live1.vcqbbs.com/16666.mp4"
				player.autoplay=this.autoplay   // 以上均为 video标签的属性配置
				//preload="preload"
				player.setAttribute("preload","preload");
				document.getElementById("myPlayer").click();
				document.getElementById("myPlayer").appendChild(player);
				console.log(this.item)
				console.log(flvjs.isSupported())
				if (flvjs.isSupported() && this.item.pull.substr(-4) == ".flv") {
					this.flvPlayer = flvjs.createPlayer({
						type: 'flv',
						isLive: true, //<====直播的话，加个这个 
						url: this.item.pull,//'http://livepull.milang.net/live/45585_1659876975.flv', //<==自行修改
					},{
						enableWorker: false, 			//不启用分离线程
						enableStashBuffer: false, 		//关闭IO隐藏缓冲区
						autoCleanupSourceBuffer: true 	//自动清除缓存
					});
					console.log(this.flvPlayer)
					this.flvPlayer.attachMediaElement(player);
					this.flvPlayer.load(); //加载
					player.play()
					this.player = player;
					console.log("isSupported")
					//this.flv_start();
					// uni.showModal({
					// 	cancelText:"1111"
					// })
					
					this.videoInterval = setInterval(() => {
						if (this.player.buffered.length) {
						let end = this.player.buffered.end(0);//获取当前时间值
						let diff = end - this.player.currentTime;//获取相差差值
						if (diff >= 1) {//如果差值大于等于0.5 手动跳帧 这里可根据自身需求来定
							this.player.currentTime = this.player.buffered.end(0);//手动跳帧
						}
					  }
					}, 5000); //5000毫秒执行一次 
				}else{
					player.src=this.item.pull;
					player.play();
					// uni.showModal({
					// 	cancelText:"222"
					// })
				}
			},
		},
		created() {
			
		},
		mounted() {
			let that = this;
			console.log("ready ...");
			this.getLivePlayer();
			
			this.$once('hook:beforeDestroy', () => {
				if(that.myVideo){
					that.myVideo.dispose();
				}
				
				if(that.videoInterval){
					clearInterval(that.videoInterval);
				}
			})
		}
	}
</script>

<style>
	@import url("common.css");
	
	.banner {
		width: 100%;
		overflow: hidden;
	}

	.banner .video {
		width: 1020px;
		height: 590px;
		background: #000000;
		border-radius: 8px;
		cursor: pointer;
	}

	.banner .playing-video {
		width: 100%;
		height: 100%;
	}

	.banner .room-num {
		top: 20px;
		right: 20px;
	}

	.video:hover .goto-video {
		display: block;
		z-index: 9999;
	}

	.banner .goto-video {
		width: 160px;
		height: 48px;
		line-height: 48px;
		background: linear-gradient(270deg, #FA58ED 0%, #FA57EC 100%);
		border-radius: 100px;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
		display: none;
	}

	.banner .anchor-active {
		border: 2px solid #FA58ED;
		position: relative;
	}

	.banner .anchor-active:before {
		position: absolute;
		top: 50%;
		transform: translateY(-50%);
		left: -7px;
		display: inline-block;
		width: 0;
		height: 0;
		content: '';
		border-right: 6px dashed #FA58ED;
		border-right: 6px solid\9 #FA58ED;
		border-top: 6px solid transparent;
		border-bottom: 6px solid transparent;

	}

	.banner .anchor-item {
		width: 140px;
		height: 140px;
		border-radius: 4px;
	}

	.banner .anchor-img {
		width: 100%;
		height: 100%;
	}

	/* æŽ¨è */
	.recommond {
		width: 100%;
		overflow: hidden;
	}

	.recommond .recommon-box {
		width: 576px;
	}

	.title-icon {
		width: 30px;
		height: 30px;
	}

	.more-img {
		width: 12px;
		height: 12px;
		margin-left: 4px;
	}

	/*åˆ—è¡¨*/
	.list-video {
		width: 100%;
		overflow: hidden;
		margin-top: 8px;
	}

	.list-video li {
		width: 184px;
		height: 242px;
		background: #FFFFFF;
		border-radius: 4px;
		overflow: hidden;
		margin-right: 12px;
		margin-top: 12px;
	}

	.list-video li>a {
		width: 100%;
		height: 100%;
	}

	.recommond .list-video li:nth-child(3n) {
		margin-right: 0;
	}

	.list-video .list-img {
		width: 184px;
		height: 184px;
		left: 0;
		top: 0;
	}

	.list-video .list-tips {
		width: 64px;
		height: 20px;
		border-radius: 2px;
		top: 12px;
		left: 12px;
	}

	.list-video .list-tips-pk {
		/*width: 64px;*/
		height: 20px;
		border-radius: 2px;
		top: 12px;
		right: 12px;
	}

	.list-video .list-content {
		width: 100%;
		height: 58px;
		padding: 12px;
		bottom: 0;
		left: 0;
	}

	.list-video .city-fans {
		margin-top: 2px;
	}

	.list-video .fans-num-icon {
		width: 12px;
		height: 12px;
		margin-right: 4px;
	}

	/*è¿è¥æ´»åŠ¨*/
	.activity {
		width: 100%;
		overflow: hidden;
	}

	.activity .activity-box {
		width: 576px;
	}

	.activity .swiper-container {
		width: 576px;
		height: 191px;
	}

	.activity .swiper-slide {
		width: 576px;
		height: 191px;
	}

	.activity .activity-img {
		width: 380px;
		height: 330px;
		border-radius: 4px;
	}

	.activity .activity-list {
		width: 184px;
		height: 102px;
		border-radius: 4px;
		margin-top: 12px;
	}

	.activity .activity-list:first-child {
		margin-top: 0;
	}

	.activity .activity-list img {
		width: 100%;
		height: 100%;
	}

	.activity .news-content {
		width: 576px;
		height: 192px;
		border-radius: 4px;
		padding: 20px 12px;
	}

	.activity .new-header {
		padding-bottom: 24px;
	}

	.activity .new-header li {
		margin-top: 30px;
	}

	.activity .new-header li:first-child {
		margin-top: 0;
	}

	.activity .news-new {
		width: 40px;
		height: 16px;
		line-height: 16px;
		background: linear-gradient(90deg, #FF7A4C 0%, #FF256D 100%);
		border-radius: 2px;
		margin-right: 8px;
	}

	.activity .new-body li {
		margin-top: 17px;
	}

	.activity .new-body li:first-child {
		margin-top: 0;
	}

	.activity .news-title {
		max-width: 450px;
	}

	/*é¢œå€¼*/
	.level-beautiful {
		width: 100%;
		overflow: hidden;
	}

	.level-beautiful .level-left-box {
		width: 772px;
	}

	.level-beautiful .level-right-box {
		width: 377px;
	}

	.level-beautiful .list-video li:nth-child(4n) {
		margin-right: 0;
	}

	.level-beautiful .rank-ul {
		padding: 12px;
		border-radius: 4px;
	}

	.level-beautiful .rank-list {
		width: 100%;
		padding: 12px 0;
	}

	.level-beautiful .rank-list a {
		width: 100%;
		height: 100%;
	}

	.level-beautiful .rank-icon {
		width: 20px;
		height: 20px;
		line-height: 20px;
	}

	.level-beautiful .rank-photo {
		width: 48px;
		height: 48px;
		margin: 0 12px;
	}

	.banner .prism-player .prism-volume-control .volume-value {
		background: #FA58ED;
	}

	.banner .prism-player .prism-volume-control .volume-cursor:hover {
		background: #FA58ED;
	}
</style>