<template>
	<view class="all">
		<view class="rk-nvue">
			<view class="video-author">
				<text class="video-author-text">{{ name }}</text>
			</view>
			<view class="video-des">
				<text class="video-des-text">{{ videoDes }}</text>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		name:'video-bottom',
		props:{
			name:{
				type:String,
				default:''
			},
			videoDes:{
				type:String,
				default:'',
			}
		}
	}
</script>

<style>
	.all{
		/* flex-direction: row; */
	}
	.rk-nvue {
		/* position: relative; */
		/* flex: 1; */
		/* flex-direction: column-reverse; */
		/* background: #2979FF; */
	}
	.video-author {
		margin-left: 10px;
	}
	.video-author-text {
		color: #FFFFFF;
		font-size: 18px;
	}
	.video-des {
		margin: 10px;
	}
	.video-des-text {
		color: #FFFFFF;
		font-size: 15px;
		text-overflow: ellipsis;
		lines:3;
	}
	
	
</style>
