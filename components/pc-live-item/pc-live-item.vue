<template>
	<view class="feed live ">
		<view class="link" @click="onItemClick">
			<image class="screenshot thumb" :src="item.thumb"></image>
			<view class="user flex-r">
				<view class="user_left fl">
					<image class="avatar thumb" :src="item.thumb"  style="display: inline-block;">
					</image>
				</view>
				<view class="user_right fl">
					<view class="username">{{item.user_nicename}}</view>
					<view class="bottom">
						<text class="type">{{item.city}}</text>
						<text class="nums"><text class="cuIcon-peoplelist f-14"></text> {{item.nums}}</text>
					</view>
				</view>
				
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		name:"pc-live-item",
		data() {
			return {
				
			};
		},
		props:{
			item:{
				type: Object,
				default(){
					return {
						
					};
				}
			},
		},
		methods:{
			onItemClick(){
				//判断是否登入
				var res = global.isLogin();
				if(!res){
					uni.showModal({
						title:'请登录',
						content:"请登录",
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
			}
		}
	}
</script>

<style>
@import url("@/common/pc/index.css");
</style>