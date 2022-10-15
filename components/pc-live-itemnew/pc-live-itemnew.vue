<template>
	<li class="common-left">
		<a class="common-block common-relative" @click="onItemClick">
			<img class="common-absolute list-img" 
				:src="item.thumb"
				:style="'width:'+imgWidth+'px;height:'+imgWidth+'px'"
				alt="">
			<div class="common-absolute list-content common-size-border">
				<p class="common-text-f14 common-text-bold common-color-23272D common-elip-1">{{item.user_nicename}}</p>
				<div
					class="common-flex common-space-between common-center-y common-text-f12 common-color-808998 city-fans">
					<span>{{item.city}}</span>
					<div class="common-flex common-center-y">
						<img class="fans-num-icon"
							src="https://qiandurebo.com/web/static/web/images/index/peoplenum.png"
							alt="">
						<span>{{item.nums}}人气</span>
					</div>
				</div>
			</div>
		</a>
	</li>
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
						avatar_thumb:"",
						thumb:"",
						user_nicename:"...",
						city:"火星",
						nums:"0",
						uid:"",
						stream: "",
						anyway: "",
					};
				}
			},
			imgWidth:{
				typeof:[String,Number],
				default(){
					return 184;
				}
			}
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
				uni.redirectTo({
					url:'/pages/pc/livepull/livepull?liveuid='+this.item.uid+'&stream='+this.item.stream+'&anyway='+this.item.anyway+'&live_nicename='+this.item.user_nicename+'&live_avatar='+this.item.avatar_thumb,
				});
			}
		}
	}
</script>

<style>
@import url("@/common/pc/rebo-common.css");
@import url("@/common/pc/rebo-index.css");
</style>