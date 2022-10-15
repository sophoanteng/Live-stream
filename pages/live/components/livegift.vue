<template>
	<!-- 礼物列表 -->
	<view class="gift_list" v-show="!gift_flag">
		<uni-icons type="close" class="close_ico" :size="25" color="#fff" @click="closeDialog()"></uni-icons>
		<view class="tit">礼物</view>
		<view class="gift_ul">
			<view class="lils" :class="curr_gift.id == item.id?'selected':''" v-for="item in list" @click="onItemClick(item)">
				<image :src="item.gifticon" mode="" class="gift_icon"></image>
				<view class="gift_name"><text class="g_n_t">{{item.giftname}}</text></view>
				<view class="gift_coin"><text class="g_c_t">{{item.needcoin}}钻石</text></view>
			</view>
		</view>
		<view class="operate">
			<view class="balance">
				<image src="../../../static/live/drill.png" class="drill"></image>
				<text class="quota">{{coin}}</text>
				<text class="charge" @click="toCharge">充值</text>
				<uni-icons type="forward" class="forward"></uni-icons>
			</view>
			<view class="give" @click="gift_give"><text class="give_t" @click="open(['slide-left'])">赠送</text></view>
		</view>
		
	</view>
</template>

<script>
	export default {
		name:"live-gift",
		props:{
			gift_flag:{
				type:Boolean,
				default:""
			},
			coin:{
				type:String,
				default:"0"
			},
			list:{
				type:Array,
				default:[]
			},
			curr_gift:{
				type:Object,
				default:{}
			},
		},
		methods:{
			closeDialog(){
				uni.$emit("transport_g")
			},
			open(e) {
				//this.$emit('open',e)
			},
			gift_give(){
				this.$emit('gift_give')
			},
			onItemClick(item){
				this.$emit('gift_change',item)
			},
			toCharge(){
				uni.showModal({
					title:"提示",
					content:"是否立即前往下载APP进行充值？",
					success(res) {
						if (res.confirm) {
							window.open('http://35.241.92.99')
						}
					}
				})
			}
		}
	}
</script>

<style>
	/* 礼物列表 */
	.gift_list .close_ico{
		font-size: 30px;
		color: #fff;
		position: absolute;
		right: 10px;
		top: 10px;
	}
	.gift_list{
		width: 100%;
		/* height: 280px; */
		background-color: #000;
		position: absolute;
		bottom: 0px;
	}
	.gift_list .tit{
		width: 40px;
		height: 40px;
		line-height: 40px;
		font-size: 16px;
		color: #fff;
		text-align: center;
		border-bottom: 2px solid #fff;
		margin-left: 10px;
	}
	.gift_list .gift_ul{
		margin-top: 20px;
		width: 98%;
		height: 200px;
		margin-left: 1%;
		display: flex;
		flex-wrap: wrap;
		overflow: auto;
		justify-content: center;
	}
	.gift_list .lils.selected{
		border: 1px solid darkgreen;
	}
	.gift_list .lils{
		width: 115px;
		height: 110px;
		padding-top: 10px;
		text-align: center;
		border: 1px solid #111111;
	}
	.gift_list .lils .gift_icon{
		width: 40px;
		height: 40px;
	}
	.gift_list .lils .gift_name{
		
		height: 20px;
		line-height: 20px;
	}
	.gift_list .lils .gift_name .g_n_t{
		font-size: 16px;
		color: #fff;
		
	}
	.gift_list .lils .gift_coin{
		
		height: 20px;
		line-height: 20px;
	}
	.gift_list .lils .gift_coin .g_c_t{
		font-size: 13px;
		color: #CCCCCC;
		
	}
	
	.operate{
		width: 98vw;
		padding: 0 1vw;
		height: 60px;
		line-height: 60px;
		position: relative;
	}
	.operate .balance .drill{
		width: 35px;
		height: 35px;
		top:8px;
	}
	.operate .balance .quota{
		font-size: 18px;
		color: #FFFFFF;
		margin-left: 6px;
	}
	.operate .balance .charge{
		font-size: 19px;
		color: #007aff;
		margin-left: 8px;
	}
	.operate .balance .forward{
		color: #FFFFFF !important;
		font-size: 18px !important;
	}
	.operate .give{
		width: 80px;
		height: 35px;
		background-color: #F36435;
		position: absolute;
		top: 10px;
		top: 13px;
		right: 1.5%;
		line-height: 30px;
		text-align: center;
		border-radius: 20px;
	}
	.operate .give .give_t{
		font-size: 16px;
		color: #FFFFFF;
	}
</style>
