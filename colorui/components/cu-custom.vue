<template>
	<view>
		<view class="cu-custom" :style="[{height:CustomBar + 'px'}]">
			<view class="cu-bar fixed" :style="style" :class="[bgImage!=''?'none-bg text-white bg-img':'',bgColor]">
				<view class="action" @tap="BackPage" v-if="isBack">
					<text class="cuIcon-back f-20"></text>
					<slot name="backText"></slot>
				</view>
				<view class="action" v-else>
					
				</view>
				<view class="content f-20" :style="contentStype">
					<slot name="content"></slot>
				</view>
				<view class="right-action">
					<slot name="right"></slot>
				</view>
				
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				StatusBar: this.StatusBar,
				CustomBar: this.CustomBar,
				menuButtonLeft:0,
			};
		},
		name: 'cu-custom',
		computed: {
			style() {
				var StatusBar= this.StatusBar;
				var CustomBar= this.CustomBar;
				var bgImage = this.bgImage;
				var style = `height:${CustomBar}px;padding-top:${StatusBar}px;`;
				if (this.bgImage) {
					style = `${style}background-image:url(${bgImage});`;
				}
				return style
			},
			contentStype(){
				var StatusBar= this.StatusBar;
				var CustomBar= this.CustomBar;
				let menuButtonInfo = null;
				//在H5中getMenuButtonBoundingClientRect报错
				// #ifndef H5
				menuButtonInfo = uni.getMenuButtonBoundingClientRect();
				// #endif
				console.log(menuButtonInfo);
				var style = `top:${StatusBar}px;`;
				if(menuButtonInfo){
					var windowWidth = this.windowWidth;
					style = `top:${StatusBar}px;width:${menuButtonInfo.left-50}px;right:${windowWidth-menuButtonInfo.left}px;left:50px;text-align: left;`;
				}
				return style;
			}
		},
		mounted:function(){
			let menuButtonInfo = null;
			// #ifndef H5
			menuButtonInfo = uni.getMenuButtonBoundingClientRect();
			// #endif
			// if(menuButtonInfo){
			// 	menuButtonLeft = menuButtonInfo.left;
			// }
		},
		props: {
			bgColor: {
				type: String,
				default: ''
			},
			isBack: {
				type: [Boolean, String],
				default: false
			},
			bgImage: {
				type: String,
				default: ''
			},
		},
		methods: {
			BackPage() {
				let pages = getCurrentPages();
				if(pages.length > 1){
					this.Common.closePage();
				}else{
					this.Common.openUrl({
						url:"/pages/index/index"
					});
				}
			}
		}
	}
</script>

<style>
.right-action{
	margin-right: 15px;
}
</style>
