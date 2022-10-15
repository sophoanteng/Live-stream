<template>
	<view class="w-full h-[100vh] items-center justify-center flex">
		<text v-if="isFailed">Something went wrong!</text>
	</view>
</template>

<script>
const app = getApp();
export default {
	data() {
		return {
			isFailed: false
		}
	},
	onLoad(e) {
		const uid = e.uid;
		const token = e.token;
		console.log(e)
		uni.request({
			url: app.globalData.site_url + "/appapi/?service=User.iftoken",
			method: "GET",
			data: {
				uid: uid,
				token: token,
			},
			success: (res) => {
				var data = res.data.data;
				console.log("here is data", data)
				if (data["code"] != 0) {
					this.isFailed = true
					console.log(" frst this.isFailed", !this.isFailed)
				} else {
					uni.setStorageSync('uid', uid);
					uni.setStorageSync('token', token);
					uni.navigateTo({
						url: '../index/index',
					});
				}
			},
		});
	}
}
</script>

<style>

</style>
