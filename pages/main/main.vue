<template>
	<view>
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			}
		},
		methods: {
			
		},
		onLoad() {
			let sysInfo = uni.getSystemInfoSync();
			if(sysInfo.windowWidth > 600){
				uni.redirectTo({
					url:"/pages/pc/index/index"
				})
			}else{
				uni.redirectTo({
					url:"/pages/index/index"
				})
			}
		}
	}
</script>

<style>

</style>
