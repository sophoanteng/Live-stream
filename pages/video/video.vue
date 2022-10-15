<template>
	<view class="">
		<video id="video_play" 
				:src="href"
				muted
				autoplay
				:controls="false"
				duration="15"
				object-fit="fill" 
		></video>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				uid: '',
				token:'',
				href:'',
				videoid:'',
			}	
		},
		onLoad(e) {
			//判断是否登入
			var res = global.isLogin();
			if(!res){
				uni.showModal({
					title:'请登录',
					content:"请登录",
					success:function(){
						uni.navigateTo({
							url:'../login/login',
						});
					}
				})
			}
			this.uid=res[0];
			this.token=res[1];
			this.videoid=e.videoid;
			uni.request({
				url: '/appapi/?service=Video.getVideo',
				method: 'GET',
				data: {
					uid:this.uid,
					videoid:this.videoid
				},
				success: res => {
					this.href=res.data.data.info[0]['href'];
					console.log(res.data.data.info[0]['href']);
				},
				fail: () => {},
				complete: () => {}
			});
			
		},
		onReady: function (res) {
		    // 获取 video 上下文 videoContext 对象
		    this.videoContext = uni.createVideoContext('video_play');
		    // 进入全屏状态
		    this.videoContext.requestFullScreen();
		},
		methods: {
			
			
		}
	}
</script>

<style>
</style>
