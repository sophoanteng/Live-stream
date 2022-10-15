<template>
  <view class="view-row-item">
  	<view class="recommended-video">
  		<view class="recommended-video-thumbnail">
  			<!-- <div class="bg-layer"/> -->
  			<div class="recommended-video-overlay">
  				<img src="../../static/play.png" alt="play" class="play-btn"/>
  			</div>
  			<div class="live-text">
  				<img src="../../static/live.svg" alt="play" />
  			</div>
  			<video :controls="false" :src="background" class="img-thumb"/>
  		</view>
  		<view class="video-info">
  		  <pcAvatar
  		    :src="avatar"
  		    class="w-2 h-2 mr-[10px]"
  		  />
  		  <view>
  			<p class="text-[18px] font-bold text-[#202124] ">{{title}}</p>
  		    <h6 class="text-[14px] font-bold text-[#5B5D6B] ">{{subtitle}}</h6></view
  		  ></view
  		>
  	</view>
    </view>
</template>

<script>
// import Video from '../../pages/video/video.vue';
import pcAvatar from "../pc-avatar/pc-avatar.vue";
export default {
  components: { pcAvatar },
  name: "pc-thumbnail",
  data() {
    return {};
  },
  props: {
    background: {
      type: String,
      default() {
        return "";
      },
    },
    avatar: {
      type: String,
      default() {
        return "";
      },
    },
    title: {
      type: String,
      default() {
        return "";
      },
    },
    subtitle: {
      type: String,
      default() {
        return "";
      },
    },
  },
  methods: {
	openUrl(url) {
		console.log('url',url)
		uni.$emit("close_socket");
		uni.navigateTo({
		  url: url
		});
	},
  },
  created() {},
};
</script>

<style>

.uni-video-cover{
	background: yellow;
}

.thumbnail-image {
  object-fit: cover;
  position: relative;
}

.thumbnail-text-container {
  display: flex;
  justify-items: center;
  padding: max(20px);
  flex: 1 1 100%;
  height: min-content;
}

.box {
	 width: 100%;
	 height: 100%;
	 position: relative;
	 display: flex;
	 flex-wrap: wrap;
}
.thumbnail {
	flex: 0 1 100%;
	position: relative;
}
.view-row-item {
	position: relative;
	flex: 0 1 32%;
}
.recommended-video {
    width: 100%;
    height: 100%;
    position: relative;
    display: flex;
    flex-wrap: wrap;
}
.recommended-video-thumbnail {
  flex: 0 1 100%;
  position: relative;
}

.recommended-video-thumbnail .img-thumb{
    /* height: 100%; */
	width: 100%;
	border-radius: 0.5em 0.5em 0 0;
}
.recommended-video-overlay {
	position: absolute;
	width: 100%;
	height: 100%;
	top: -5%;
	left: 0;
	color: #fff;
}
.play-btn {
	position: absolute;
	width:100%;
	top: 50%;
	left: 50%;
	transform: translate(-50%, -50%);
	visibility: hidden;
	z-index: 3;
}
.bg-layer {
	background-color: rgb(22 26 26 / 0.3);
	position: absolute;
	width: 100%;
	height: 100%;
	visibility: hidden;
}
.recommended-video-thumbnail:hover .bg-layer, .recommended-video-thumbnail:hover .play-btn{
	cursor: pointer;
	visibility: visible;
	transition: 0.5s all;
}
.live-text {
	position: absolute;
	margin: 0.5em;
	z-index: 10;
}
.video-info {
	font-size: 12px;
	flex: 0 1 100%;
	display: flex;
	background: white;
	padding: 1em 0.5em;
	border-radius: 0 0 0.5em 0.5em;
	margin-top: -0.5em;
}
</style>
