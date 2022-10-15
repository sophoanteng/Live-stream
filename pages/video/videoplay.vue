<template>
	<view class="warp">
		<!-- 播放 -->
		<video class="rk-video" :src="href" controls="false" autoplay="true" loop="true" show-play-btn="false" show-center-play-btn="false" muted="false">
		</video>
		<!-- 非app显示UI -->
		<view class="rk-video-layer">
			<view class="rk-back">
				<u-icon class="rk-back-icon" name="arrow-left" color="#fff" size="50" @click="clickBack">返回</u-icon>
			</view>
			<!-- 右边 -->
			<video-right class="rk-vright" :icon="user_avatar" :comments="videoCom" :shares="videoShare" :like="videoLike" :attent="userAttent"  @attClick="attClick" @likeClick="likeClick" @shareClick="shareClick">
			</video-right>

			<!-- 底部视频信息 -->
			<video-bottom class="rk-videoauthor" :name="user_name" :videoDes="title">

			</video-bottom>

			<!-- 评论框 -->
			<view class="rk-footer">
				<video-input @send-message="getInputMessage" v-model="videoVal" :inputVal="videoVal"></video-input>
			</view>
		</view>
	


	</view>
</template>

<script>
	// 非 NVUE
	import videoInput from "./components/videoinput.vue";
	import videoBottom from "./components/videobottom.vue"
	import videoRight from "./components/videoright.vue"
	const app = getApp();
	export default {
		data() {
			return {
				uid:'',
				token:'',
				user_avatar: "",
				user_name: '',
				
				videoid:'',
				videoVal: '',
				
				
				title: '',
				
				videoCom: '0',
				videoShare: '0',
				videoLike: '0',
				islike: true,
				userAttent: true,
				href: "",
				
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
			
			//获取视频详情页
			uni.request({
				url: app.globalData.site_url +'/appapi/?service=Video.getVideo',
				method: 'GET',
				data: {
					uid:this.uid,
					videoid:this.videoid
				},
				success: res => {
					var data=res.data.data;
					this.href=data.info[0]['href'];
					this.user_name=data.info[0]['user_nicename'];
					this.title=data.info[0]['title'];
					this.user_avatar=data.info[0]['userinfo']['avatar'];
					
					this.videoShare=data.info[0]['shares'];
					this.videoLike=data.info[0]['likes'];
					this.videoCom=data.info[0]['comments'];
					console.log(res.data.data.info[0]);
				},
				fail: () => {},
				complete: () => {}
			});
		},
		onUnload() {
		
		},
		onShow() {
			console.log('vvvuuuueee');
			
		},
		methods: {
			getInputMessage: function(message) {
				//获取子组件的输入数据
				console.log(message);
				// this.videoVal = '';
			},
			clickBack() {
				uni.navigateBack({});
			},
			attClick() {
				this.userAttent = !this.userAttent;
			},
			likeClick() {
				this.islike = !this.islike;
				if (this.islike) {
					this.videoLike += 1;
				} else {
					this.videoLike -= 1;
					
				}
				console.log(this.islike);
			},
			shareClick(){
				
			}

		},
		components: {
			// 非NVUE
			
			videoInput,
			videoBottom,
			videoRight,
		
		}
	}
</script>

<style>
	.rk-video {
		display: flex;
		flex-direction: column;
		transition: all 0.5s;
		height: 100vh;
		width: 100vw;
		overflow: hidden;
		background-color: #19BE6B;
	}

	.controls-title {
		margin-top: 50px;
		width: 100%;
		text-align: center;
		color: #FFFFFF;
	}

	.rk-video-layer {
		display: flex;
		flex-direction: column;
		transition: all 0.5s;
		height: 100vh;
		width: 100vw;
		position: fixed;
		z-index: 1;
	}

	.rk-back {
		width: 36px;
		height: 36px;
		border-radius: 18px;
		background-color: rgba(0, 0, 0, 0.3);
		position: fixed;
		left: 15px;
		top: 25px;

		display: flex;
		justify-content: center;
	}

	.rk-back-icon {
		margin-left: -5px;
		/* background-color: #18B566; */
	}

	.rk-vright {
		/* background-color: #FF0000; */
		width: 20vw;
		position: fixed;
		right: 0;
		bottom: 100rpx;
		/* display: flex;
		flex-direction: column; */
	}

	.rk-videoauthor {
		/* background-color: #18B566; */
		width: 80vw;
		position: fixed;
		bottom: 100rpx;
		display: flex;
		flex-direction: column;
	}

	.rk-footer {
		width: 100vw;
		position: fixed;
		bottom: 0rpx;
		/* background-color: #18B566; */
	}
</style>
