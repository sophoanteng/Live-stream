<template>
	<view>
		<pc-topbar/>
		<view class="grid-container">
			<pc-group-thumbnail :items="items" :title="title"/>
			<pc-sidebar/>
		</view>
	</view>
</template>

<script>
	const app = getApp();
	export default {
		data() {
			return {
				categoryId: '',
				title: '',
				items: []
			}
		},
		onLoad(e) {
			this.categoryId = e.id
			this.title = e.title
			uni.request({
				url: app.globalData.site_url +'/appapi/?service=Home.getClassLive',
				method: 'GET',
				data: {
					liveclassid:this.categoryId,
					p: 1,
				},
				success: res => {
					let liveclass = res.data.data.info;
					console.log('liveclassListtttt',liveclass)
					this.items = liveclass
					console.log('this.items',this.items)
				},
				fail: () => {},
				complete: () => {}
			});
		},
		methods: {
			getPageData() {
				
			}
		}
	}
</script>

<style>
	.grid-container {
		position: relative;
		width: calc(100vw - 350px);
		margin-right: 350px;
		padding-top: 92px;
		padding-bottom: 3rem;
		display: flex;
		justify-content: center;
		align-content: flex-start;
		flex-wrap: wrap;
	}
</style>
