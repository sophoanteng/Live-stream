<template>
	<view class="footer">
		<view class="footer-left">
			<input class="input-text" :focus="false" type="text" v-bind:value="inputVal"
				@input="$emit('input', $event.target.value)" placeholder="说点什么..." :maxlength="250" confirm-type="send"
				@keydown.enter="sendMessge" confirm-hold />
		</view>
		<view class="footer-right">
			<image class="right-img" src="../../../static/video/video_face.png" mode="aspectFit"></image>
		</view>
	</view>

</template>

<script>
	export default {
		name: "video-input",
		model: {
			prop: 'inputVal',
			event: 'input'
		},
		props: {
			inputVal: [String, Number]
		},

		methods: {
			sendMessge() {
				var that = this;
				if (that.inputVal.trim() === "") {
					uni.showToast({
						icon: 'none',
						title: '请输入内容'
					});
					return;
				}
				if (that.inputVal.trim() === "") {
					this.$emit('input', "");
					console.log('111111');
				} else {
					this.$emit("send-message", {
						type: "text",
						content: that.inputVal,
					});
					this.$emit('input', "");
					console.log('222222');
				}
			}
		}
	}
</script>
<style>
	.footer {
		
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		
		flex: 1;
		flex-direction: row;
		background-color: rgba(48,49,51,0.3);
	}

	.footer-left {
		flex: 1;
	}

	.input-text {
		flex: 1;
		background: #f5f4f7;
		height: 70rpx;
		border-radius: 35rpx;
		margin: 15rpx 0rpx 15rpx 15rpx;
		padding-left: 30rpx;
		font-size: 32rpx;
	}

	.footer-right {
		
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		
		justify-content: center;
		align-items: center;
		padding-left: 20rpx;
		padding-right: 20rpx;
		/* background-color: #18B566; */
		
	}

	.right-img {
		width: 60rpx;
		height: 60rpx;
		align-items: center;
		/* background-color: #303133; */
	}
</style>
