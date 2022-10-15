<template>
	<view>
		<!-- <video id="videoElement" class="centeredVideo" x5-video-player-type='h5-page' controls autoplay width="1024" height="576">Your -->
			<!-- browser is too old which doesn't support HTML5 video.</video> -->
		<view id="myPlayer">
			
		</view>	
		<button @click="flv_start()">开始</button>
		<button @click="flv_pause()">暂停</button>
		<button @click="flv_destroy()">停止</button>
		<button @click="flv_seekto()">跳转</button>

	</view>
</template>

<script>

	export default {
		data() {
			return {
				enableProgressGesture: false,
				autoplay: true,
				showPlayBtn: false,
				showCenterPlayBtn: false,
				showFullscreenBtn: false,
				controls: false,
				windowWidth: '',
				windowHeight: '',
				flvPlayer: null,
				player: null,
				"rtmpUrl": "rtmp://play.jyzqd.com/shoucqApp/user60?auth_key=1614590810-0-0-cb3d750e0de978cde317f746d0d660cc",
				    "flvUrl": "http://play.jyzqd.com/shoucqApp/user60.flv?auth_key=1614590810-0-0-cb4508a7bd12cf62af314646b821257d",
				    "m3u8Url": "http://play.jyzqd.com/shoucqApp/user60.m3u8?auth_key=1614590810-0-0-692d0bc2e21190541101bf468db28273"
			};
		},onLoad() {
			let sysInfo = uni.getSystemInfoSync()
			this.windowWidth = sysInfo.screenWidth
			this.windowHeight = sysInfo.windowHeight
		},
		mounted() {
			// #ifdef H5

			// #endif
			this.$nextTick(() => {
				this.getLivePlayer()
			})
			console.log(flvjs)
		},
		methods: {
			getLivePlayer() {  // 生成需要的video 组件
				// var player = document.getElementById('videoElement');
				var player = document.createElement('video')
				player.id = 'video'
				player.style = 'width:' + this.windowWidth + 'px;height: '+ (this.windowHeight-60) + 'px'
				player.enableProgressGesture = this.enableProgressGesture
				player.controls=this.controls
				player.showCenterPlayBtn=this.showCenterPlayBtn
				player.showPlayBtn=this.showPlayBtn
				player.showFullscreenBtn=this.showFullscreenBtn
				player.x5VideoPlayerType='h5-page'
				player.x5VideoPlayerFullscreen="false" 
				player.src="http://live1.vcqbbs.com/16666.mp4"
				player.autoplay=this.autoplay   // 以上均为 video标签的属性配置
				document.getElementById("myPlayer").appendChild(player);
				console.log(player)
				console.log(flvjs.isSupported())
				if (flvjs.isSupported() && false) {
					this.flvPlayer = flvjs.createPlayer({
						type: 'flv',
						isLive: true, //<====直播的话，加个这个 
						url: 'http://livepull.milang.net/live/45585_1659876975.flv', //<==自行修改
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
					uni.showModal({
						cancelText:"1111"
					})
				}else{
					uni.showModal({
						cancelText:"222"
					})
				}
			},
			flv_start() {   //开始
				console.log(this.player)
				this.flvPlayer.play()
			},
			flv_pause() {  //暂停
				this.player.pause();
			},
			flv_destroy() { //停止
				this.player.pause();
				this.player.unload();
				this.player.detachMediaElement();
				this.player.destroy();
				this.player = null;
			},
			flv_seekto() {   // 复制其他人的  我还没用这个
				this.player.currentTime = parseFloat(document.getElementsByName('seekpoint')[0].value);
			},
		}
	}
</script>

<style lang="scss">

</style>

