<template>
	<view class="banner common-min-width">
		<div class="common-wrapper clearfix common-mar-t-sm">
			<div class="video common-relative common-left">
				<div class="playing-video prism-player" id="id_video" x5-playsinline="" x-webkit-airplay=""
					playsinline="" webkit-playsinline="" style="width: 100%; height: 100%;">
					<view class="uni-video-container" >
						<pclive-play boxWidth="1020" boxHeight="590" :item="currItem" v-if="currItem"></pclive-play>
					</view>
				</div>
				
			</div>
			<div class="common-right banner-anchor">
				<ul>
					<block v-for="(item,index) in list">
						<li class="anchor-item common-size-border" v-if="index < 4" :class="currItem && currItem.uid == item.uid ? 'anchor-active':''"
						@click="onGroupItemClick(item)">
							<img class="anchor-img"
								:src="item.thumb"
								src_="https://imgcdn.qiandurebo.com//static_data/showcover/new/20220518/19682479_776.jpg?imageView2/2/h/600"
								:alt="item.user_nicename">
						</li>
					</block>
				</ul>
			</div>
		</div>
	</view>
</template>

<script>
	export default {
		name:"pclive-group",
		data() {
			return {
				currItem:null
			};
		},
		props:{
			list:{
				type:Array,
				default(){
					return [];
				}
			}
		},
		created() {
			console.log(this.list);
			if(this.list && this.list.length > 0){
				this.currItem = this.list[0];
			}
		},
		methods:{
			onGroupItemClick(item){
				console.log(item);
				console.log(this.currItem);
				let that = this;
				if(item.uid == this.currItem.uid){
					return;
				}
				this.currItem = null;
				
				setTimeout(function(){
					that.currItem = item;
				},500);
			}
		}
	}
</script>

<style>
	@import url("common.css");
	.banner-anchor ul li:nth-of-type(n+2){
		margin-top: 10px;
	}
	.banner {
		width: 100%;
		overflow: hidden;
	}

	.banner .video {
		width: 1020px;
		height: 590px;
		background: #000000;
		border-radius: 8px;
		cursor: pointer;
	}

	.banner .playing-video {
		width: 100%;
		height: 100%;
	}

	.banner .room-num {
		top: 20px;
		right: 20px;
	}

	.video:hover .goto-video {
		display: block;
		z-index: 9999;
	}

	.banner .goto-video {
		width: 160px;
		height: 48px;
		line-height: 48px;
		background: linear-gradient(270deg, #FA58ED 0%, #FA57EC 100%);
		border-radius: 100px;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
		display: none;
	}

	.banner .anchor-active {
		border: 2px solid #FA58ED;
		position: relative;
	}

	.banner .anchor-active:before {
		position: absolute;
		top: 50%;
		transform: translateY(-50%);
		left: -7px;
		display: inline-block;
		width: 0;
		height: 0;
		content: '';
		border-right: 6px dashed #FA58ED;
		border-right: 6px solid\9 #FA58ED;
		border-top: 6px solid transparent;
		border-bottom: 6px solid transparent;

	}

	.banner .anchor-item {
		width: 140px;
		height: 140px;
		border-radius: 4px;
	}

	.banner .anchor-img {
		width: 100%;
		height: 100%;
	}

	/* ???????????? */
	.recommond {
		width: 100%;
		overflow: hidden;
	}

	.recommond .recommon-box {
		width: 576px;
	}

	.title-icon {
		width: 30px;
		height: 30px;
	}

	.more-img {
		width: 12px;
		height: 12px;
		margin-left: 4px;
	}

	/*?????????????*/
	.list-video {
		width: 100%;
		overflow: hidden;
		margin-top: 8px;
	}

	.list-video li {
		width: 184px;
		height: 242px;
		background: #FFFFFF;
		border-radius: 4px;
		overflow: hidden;
		margin-right: 12px;
		margin-top: 12px;
	}

	.list-video li>a {
		width: 100%;
		height: 100%;
	}

	.recommond .list-video li:nth-child(3n) {
		margin-right: 0;
	}

	.list-video .list-img {
		width: 184px;
		height: 184px;
		left: 0;
		top: 0;
	}

	.list-video .list-tips {
		width: 64px;
		height: 20px;
		border-radius: 2px;
		top: 12px;
		left: 12px;
	}

	.list-video .list-tips-pk {
		/*width: 64px;*/
		height: 20px;
		border-radius: 2px;
		top: 12px;
		right: 12px;
	}

	.list-video .list-content {
		width: 100%;
		height: 58px;
		padding: 12px;
		bottom: 0;
		left: 0;
	}

	.list-video .city-fans {
		margin-top: 2px;
	}

	.list-video .fans-num-icon {
		width: 12px;
		height: 12px;
		margin-right: 4px;
	}

	/*????????????????????????*/
	.activity {
		width: 100%;
		overflow: hidden;
	}

	.activity .activity-box {
		width: 576px;
	}

	.activity .swiper-container {
		width: 576px;
		height: 191px;
	}

	.activity .swiper-slide {
		width: 576px;
		height: 191px;
	}

	.activity .activity-img {
		width: 380px;
		height: 330px;
		border-radius: 4px;
	}

	.activity .activity-list {
		width: 184px;
		height: 102px;
		border-radius: 4px;
		margin-top: 12px;
	}

	.activity .activity-list:first-child {
		margin-top: 0;
	}

	.activity .activity-list img {
		width: 100%;
		height: 100%;
	}

	.activity .news-content {
		width: 576px;
		height: 192px;
		border-radius: 4px;
		padding: 20px 12px;
	}

	.activity .new-header {
		padding-bottom: 24px;
	}

	.activity .new-header li {
		margin-top: 30px;
	}

	.activity .new-header li:first-child {
		margin-top: 0;
	}

	.activity .news-new {
		width: 40px;
		height: 16px;
		line-height: 16px;
		background: linear-gradient(90deg, #FF7A4C 0%, #FF256D 100%);
		border-radius: 2px;
		margin-right: 8px;
	}

	.activity .new-body li {
		margin-top: 17px;
	}

	.activity .new-body li:first-child {
		margin-top: 0;
	}

	.activity .news-title {
		max-width: 450px;
	}

	/*?????????????*/
	.level-beautiful {
		width: 100%;
		overflow: hidden;
	}

	.level-beautiful .level-left-box {
		width: 772px;
	}

	.level-beautiful .level-right-box {
		width: 377px;
	}

	.level-beautiful .list-video li:nth-child(4n) {
		margin-right: 0;
	}

	.level-beautiful .rank-ul {
		padding: 12px;
		border-radius: 4px;
	}

	.level-beautiful .rank-list {
		width: 100%;
		padding: 12px 0;
	}

	.level-beautiful .rank-list a {
		width: 100%;
		height: 100%;
	}

	.level-beautiful .rank-icon {
		width: 20px;
		height: 20px;
		line-height: 20px;
	}

	.level-beautiful .rank-photo {
		width: 48px;
		height: 48px;
		margin: 0 12px;
	}

	.banner .prism-player .prism-volume-control .volume-value {
		background: #FA58ED;
	}

	.banner .prism-player .prism-volume-control .volume-cursor:hover {
		background: #FA58ED;
	}
</style>