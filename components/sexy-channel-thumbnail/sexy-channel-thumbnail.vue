<template>
	<div id="left" class="group-container column">
	<div class="title">{{title}}</div>
		<div v-for="item in classLiveList.slice(0, this.countShow)" :key="item.id" class="items" @click="openUrl(`/pages/pc/live/index?liveuid=${item.uid}&stream=${item.stream}&anyway=${item.anyway}&live_nicename=${item.user_nicename}&live_avatar=${item.avatar_thumb}&flv=${item.pull}`)">
			<pc-thumbnail 
				:background="item.pull" 
				:avatar="item.avatar" 
				:title="item.title" 
				:subtitle="item.city"
			/>
		</div>
	<!-- <pc-show-more v-if="items.length > 0"/> -->
		<h2 class="show-more" @click="loadMore"><span>Show More</span></h2>
</div>
</template>

<script>
export default {
	name:"sexy-channel-thumbnail",
	props: ['classLiveList', 'title'],
	data() {
	  return {
		countShow: 3,
	  }
	},
	methods: {
		loadMore() {
		    this.countShow += 3
	},
	openUrl(url) {
				console.log('url',url)
				uni.$emit("close_socket");
				uni.navigateTo({
					url: url
				});
			},
}
}
</script>

<style>
/* show more style */
.show-more {
	width: 100%; 
	text-align: center; 
	border-bottom: 1px solid lightgray; 
	line-height: 0.1em;
	margin: 10px 0 20px;
	cursor: pointer;
}
.show-more span {
	background: #f1f1f1;
	padding:0 50px;
	font-weight: 700;
}
/* end show more style */
.group-container {
	padding-bottom: 1em;
	flex: 0 1 95%;
	display: flex;
	justify-content: space-between;
	align-items: flex-start;
	flex-wrap: wrap;
}
.items {
	position: relative;
	flex: 0 1 49%;
	margin-bottom: 1em;
}
.title {
	flex: 0 1 100%;
	margin-bottom: 10px;
	color: #D772FB;
	font-size: 2em;
	font-weight: 700;
}

@media screen and (min-width: 1440px) {
	.items {
		position: relative;
		flex: 0 1 49%;
		margin-bottom: 1em;
		cursor: pointer;
	}
	.title {
		flex: 0 1 100%;
		margin-bottom: 10px;
		color: #D772FB;
		font-size: 2em;
		font-weight: 700;
	}
	
	@media screen and (min-width: 1440px) {
		.items {
			flex: 0 1 32%;
		}
	}
}
</style>