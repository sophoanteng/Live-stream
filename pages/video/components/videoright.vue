<template>
	<view class="all">
		<image class="uhead" :src="icon" mode="aspectFit"></image>
		
		<view class="attent" @tap="attClick">
			<image class="attent-sub" v-show="attent" src="../../../static/video/video_follow.png" mode="aspectFit"></image>
		</view>
		<view class="item" @tap="likeClick">
			<image class="item-img" v-if="!islike" src="../../../static/video/video_zan_sel.png" mode="aspectFit"></image>
			<image class="item-img" v-else src="../../../static/video/video_zan.png" mode="aspectFit"></image>
			<text class="item-title"> {{like}} </text>
		</view>
		
		<view class="item" @tap="shareClick">
			<image class="item-img" src="../../../static/video/video_comment.png" mode="aspectFit"></image>
			<text class="item-title"> {{comments}} </text>
		</view>
		
		<view class="item">
			<image class="item-img" src="../../../static/video/video_share.png" mode="aspectFit"></image>
			<text class="item-title"> {{shares}} </text>
		</view>
		
	</view>
</template>

<script>
	export default {
		name:'video-right',
		props:{
			icon:{
				type:String,
				default:''
			},
			attent:{
				type:Boolean,
				default:false,
			},
			like:{
				type:String,
				default:0
			},
			islike:{
				type:Boolean,
				default:false,
			},
			comments:{
				type:String,
				default:0,
			},
			shares:{
				type:String,
				default:0,
			}
		},
		methods:{
			attClick() {
				this.$emit('attClick', this.index)
			},
			likeClick() {
				this.$emit('likeClick', this.index)
			},
			shareClick() {
				this.$emit('shareClick', this.index)
			},
		}
	}
</script>

<style>
	.all {
		
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		
		flex-direction: column;
		align-items: center;
		
	}
	.uhead {
		width: 40px;
		height: 40px;
		border-radius: 20px;
		background-color: #000000;
		
	}
	.attent {
		
		position: relative;
		top: -10px;
		width: 20px;
		height: 20px;
		/* background-color: #19BE6B; */
		align-items: center;
	}
	.attent-sub {
		
		width: 20px;
		height: 20px;
	}
	.item {
		
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		
		width: 60px;
		height: 60px;
		/* background-color: #18B566; */
		flex-direction: column;
		align-items: center;
		margin-bottom: 10px;

	}
	.item-img {
		width: 36px;
		height: 36px;
		top: 0px;
	}
	.item-title {
		color: #FFFFFF;
		font-size: 15px;
		position: relative;
	}
</style>
