<template>
	<!-- 直播间观众席和主播信息 -->
	<view class="live_user">
		<view class="user">
			<!-- 主播头像 -->
			<view class="thumd">
				<image :src="live_avatar" class="thumd_t" mode=""></image>
			</view>
			<!-- 主播名称-->
			<view class="info">
				<view class="uname">
					<text class="uname_text">{{live_nicename}}</text>
				</view>
				<view class="uid">
					<text class="uid_text">ID:{{liveuid}}</text>
				</view>
			</view>
			<!-- 关注 -->
			<!-- <view class="follow">
				<text class="follow_text">关注</text>
			</view> -->
		</view>
		
		<!-- 观众席 -->
		<scroll-view scroll-x="true" class="audience" show-scrollbar="false">
			<view class="thumd" v-for="(item,audience) in audience">
				<image :src="item.avatar_thumb" class="thumd_t" mode=""></image>
			</view>
			
		</scroll-view>

	</view>
		
	
</template>

<script>
	export default {
		name:"livepull-top",
		props:{
			liveuid:{
				type:String,
				default:""
			},
			live_nicename:{
				type:String,
				default:""
			},
			live_avatar:{
				type:String,
				default:""
			},
			audience:{
				type: Array,
				default () {
					return [0, 0]
				}
			},
		}
	}
</script>

<style>
	.live_user{
		width: 750px;
		height:44px;
		position: absolute;
		/* #ifdef H5 */
		top:10px;
		/* #endif */
		/* #ifdef APP-NVUE */
		top:50px;
		/* #endif */
		left: 10px;
	}
	.live_user .user{
		width: 174px;
		/* #ifdef H5 */
		height:40px;
		background-color: rgba(0 0 0 / 0.3);
		/* #endif */
		
		/* #ifdef APP-NVUE */
		height:42px;
		background-color: #000000;
		/* #endif */
		border-radius: 40px;
		padding: 2px;
		position: relative;
	}
	.live_user .user .thumd{
		/* #ifdef H5 */
		width: 40px;
		height:40px;
		/* #endif */
		
		/* #ifdef APP-NVUE */
		width: 38px;
		height:38px;
		/* #endif */
		
		position: relative;
	}
	.live_user .user .thumd .thumd_t{
		/* #ifdef H5 */
		width: 40px;
		height:40px;
		border-radius: 40px;
		/* #endif */
		
		/* #ifdef APP-NVUE */
		width: 38px;
		height:38px;
		border-radius: 38px;
		/* #endif */
		
		position: absolute;
	}
	.live_user .user .info{
		position: absolute;
		top:0;
		left: 50px;
		
	}
	.live_user .user .info .uname{
		height: 20px;
		line-height: 20px;
	}
	.live_user .user .info .uname .uname_text{
		width: 80px;
		color: #FFFFFF;
		font-size: 16px;
		overflow: hidden;
	}
	.live_user .user .info .uid{
		height: 20px;
		line-height: 20px;
	}
	.live_user .user .info .uid .uid_text{
		font-size: 14px;
		color: #FFFFFF;
	}
	.live_user .user .follow{
		width: 45px;
		height: 30px;
		text-align: center;
		border-radius: 40px;
		background-color: #e68540;
		position: absolute;
		top: 7px;
		right: 3px;
	}
	
	.live_user .user .follow .follow_text{
		width: 45px;
		height: 30px;
		line-height: 30px;
		text-align: center;
		font-size: 16px;
		color: #fff;
	}
	.live_user .audience{
		position: absolute;
		top: 2px;
		height: 40px;
		
		
		/* #ifdef H5 */
		left: 185px;
		line-height: 40px;
		width: calc(100% - 175px);
		white-space: nowrap; 
		/* #endif */
		
		/* #ifdef APP-NVUE */
		left: 180px;
		width: 170px;
		flex: 1;
		flex-direction:row;
		flex-wrap:nowrap;
		justify-content: flex-start;
		align-items: center;
		/* #endif */
		
	}
	/* #ifdef H5 */
	.live_user .audience::-webkit-scrollbar {
		width: 0;
		height: 0;
		display: none;
	}
	
	/* #endif */
	
	.live_user .audience .thumd{
		/* #ifdef H5 */
		position: relative;
		display: inline-block;
		/* #endif */
		width: 40px;
		height:40px;
		margin-right: 5px;

		
		
	}
	.live_user .audience .thumd .thumd_t{

		width: 40px;
		height:40px;
		border-radius: 40px;
	}
</style>
