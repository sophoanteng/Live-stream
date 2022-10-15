<template>
	<view class="chat_cont">
		<!-- 聊天 -->
		<view class="live_chat">
			<scroll-view id="chat_hall" class="chat_hall" scroll-y="true" show-scrollbar="false" :scroll-top="scrollTop" :scroll-into-view="chatid">
				<view class="chat_v"  v-for="(item,index) in chat_item" :id="'chat'+index">
					<text class="chat_t"  v-if="item.method=='SystemNot'">{{item.content}}</text>
					<view class="info" v-else>
						<text class="uname">{{item.uname}}:</text>
						<text class="word_cont">{{item.content}}</text>
					</view>
				</view>				
			</scroll-view>
		</view>
		
		<!-- 公屏聊天输入框 -->
		<view class="footer" v-show="!flag">
			<uni-icons type="close" class="close_ico" :size="25" color="#fff" @click="closeDialog()"></uni-icons>
			<view class="footer-left">
				<input class="input-text" type="text" v-bind:value="livechat"
					@input="livechat_input" placeholder="说点什么..." :maxlength="250" @keydown.enter="send()" />

			</view>
			<view class="footer-right" @click="send">
				<text class="" style="color:#fff;">发送</text>
			</view>
		</view>
		
		<!-- 图标 -->
		<view class="botom_icon" v-show="flag">
			<uni-icons type="chat" class="chat_ico" @click="transport()"></uni-icons>
			<image  src="../../../static/live/gift.png" class="chat_ico gift_icon" @click="gift_icon"></image>
			<!-- <uni-icons type="close" class="close_ico" @click="goback()"></uni-icons> -->
		</view>
		
	</view>
	
</template>

<script>
	export default {
		name:"livepull-chat",
		props:{
			flag:{
				type:Boolean,
				default:""
			},
			livechat:{
				type:String,
				default:""
			},
			chat_item:{
				type: Array,
				default () {
					return [0, 0]
				}
			},
			scrollTop:{
				type:Number,
				default:9
			},
			chatid:{
				type:String,
				default:""
			},
		},
		methods:{
			closeDialog(){
				uni.$emit("transport_g")
			},
			livechat_input(e) {
				this.$emit('input',e)
			},
			send() {
				console.log('click');
				this.$emit('click')
			},
			transport() {
				this.$emit('transport')
			},
			goback() {
				this.$emit('goback')
			},
			gift_icon(){
				this.$emit('gift_icon')
			}
		}
	}
</script>

<style>
	.footer .close_ico{
		font-size: 30px;
		color: #fff;
		position: absolute;
		right: 10px;
		top: -20px;
	}
	.chat_cont{
		display: flex;
		
	}
	.live_chat{
		/* #ifdef H5 */
		position: absolute;
		/* #endif */
		/* #ifdef APP-NVUE */
		position: fixed;
		/* #endif */
		bottom: 55px; 
		width: 95%;
		height: 60vh;
		
		
	}
	.chat_hall{
		/* #ifdef APP-NVUE */
		position: absolute;
		bottom: 0px;
		/* #endif */
		
		width: 100%;
		height: 60vh;
		margin-left: 10px;
	}
	.chat_hall .chat_v{
		line-height: 40px;
	}
	.chat_t{
		/* #ifdef H5 */
		width:auto;
		display:inline-block !important;
		display:inline; 
		background-color: rgb(0 0 0 / 50%);
		/* #endif */
		
		
		/* #ifdef APP-NVUE */
		width:230px;
		background-color: #000000;
		margin-bottom: 5px;
		/* #endif */
		color: #e68540;
		line-height: 20px;
		font-size: 16px;
		padding: 6px;
		border-radius: 8px;
	}
	.info{
		/* #ifdef H5 */
		width:auto;
		display:inline-block !important;
		display:inline; 
		background-color: rgb(0 0 0 / 50%);
		line-height: 20px;
		/* #endif */
		
		/* #ifdef APP-NVUE */
		width:230px;
		background-color: #000000;
		margin-bottom: 5px;
		flex-direction: row;
		/* #endif */
		
		padding: 6px;
		border-radius: 8px;
	}
	
	.uname{
		font-size: 16px;
		color: RGB(157, 201, 255);
		/* #ifdef H5 */
		margin-right: 5px;
		/* #endif */
		
	}
	
	.word_cont{
		
		font-size: 16px;
		color: #FFFFFF;
	}
	
	
	/* 输入框 */
	.footer {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		position: absolute;
		bottom: 0px;
		width: 100%;
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
		color: #000000;
	}
	
	.footer-right {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		
		justify-content: center;
		align-items: center;
		padding-left: 20rpx;
		padding-right: 20rpx;
	}
	/* 输入框 */
	
	
	/* 按钮 */
	.botom_icon{
		/* #ifdef H5 */
		position: absolute;
		bottom: 0px;
		width: 96%;
		margin: 0 2%;
		display: flex;
		/* #endif */
		
		
	
		/* #ifdef APP-NVUE */
		position: absolute;
		bottom: 100px;
		width: 1000px;
		background-color: #FFFFFF;
		display: flex;
		/* #endif */
		
		flex-direction: row;
		justify-content:space-between;
		height:50px;
		
		
	}
	.botom_icon .chat_ico{
		/* #ifdef APP-NVUE */
		position: absolute;
		left: 10px;
		line-height: 50px;
		/* #endif */
		color: #FFFFFF !important;
		font-size: 25px !important;
	}
	
	.gift_icon{
		width: 25px;
		height: 25px;
		position: absolute;
		right: 10px;
	}
	
	.botom_icon .close_ico{
		/* #ifdef APP-NVUE */
		position: absolute;
		right: 10px;
		line-height: 50px;
		/* #endif */
		color: #FFFFFF !important;
		font-size: 25px !important;
	}
</style>
