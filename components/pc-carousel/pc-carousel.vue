<template>
	<view id="app" class="wrapper">
		<view class="box">
			<carousel-3d :controls-visible="true" v-if="recommendLists.length > 0">
				<slideArrow v-for="(slide, i) in recommendLists" :index="i" :key="i">
					<view class="box-live">
						<view class="circle-dot"></view>
						<span>LIVE</span>
					</view>
					<video class="video-slider" :src="slide.pull" />
				</slideArrow>
			</carousel-3d>
		</view>
	</view>
</template>

<script>
import slideArrow from './slideArrow.vue'
import Carousel3d from './Carousel3d.vue'
const app = getApp();
export default {
	name: 'pc-carousel',
	components: {
		Carousel3d,
		slideArrow
	},
	props: ['recommendLists'],
	data() {
		return {
			slideCount: 10
		}
	},
	methods: {
		onSlideChanged(index) {
			console.log('onSlideChanged Callback Triggered', 'Slide Index ' + index)
		},
		onLastSlide(index) {
			console.log('onLastSlide Callback Triggered', 'Slide Index ' + index)
		},
		onMainSlideClick() {
			console.log('onMainSlideClick Callback Triggered')
		},
		onAfterSlideChanged(index) {
			console.log('@after-slide-changed Callback Triggered', 'Slide Index ' + index)
		},
		onSlideChange(index) {
			console.log('@before-slide-change Callback Triggered', 'Slide Index ' + index)
		},
		addSlide() {
			this.slideCount++
		},
		removeSlide() {
			if (this.slideCount > 1) {
				this.slideCount--
			}
		},
	}
}
</script>

<style>
#app {
	font-family: Avenir, Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	text-align: center;
	color: #2c3e50;
}

uni-video {
	width: 100%;
	height: 100%;
}

.box {
	/*width: 100%;*/
	width: calc(100vw - 350px);
	/* width: 1215px; */
	height: 500px;
	display: flex;
	flex: 0 1 100%;
	justify-content: center;
	align-items: center;
}

.carousel-3d-slide img {
	width: 100%;
	height: 100%;
	object-fit: cover;
}

.box-live {
	position: relative;
	display: flex;
	justify-content: center;
	align-items: center;
	margin-top: 7px;
	padding: 4px 9px;
	margin-left: 10px;
	border-radius: 3px;
	width: 86px;
	z-index: 40;
	position: absolute;
	background: #FB4A59;
}

.circle-dot {
	background: white;
	clip-path: circle(50% at 50% 50%);
	width: 9px;
	margin-right: 10px;
	height: 9px;
}

.box-live span {
	color: white;
	font-size: 18px;
	margin-right: 3px;
	margin-left: -3px;
}

.carousel-3d-slide.current {
	width: 899px !important;
	height: 418px !important;
	border-radius: 6px;
	right: -79% !important;
}

.carousel-3d-container {
	overflow: inherit;
	bottom: 17%;
}

.carousel-3d-slider {
	right: 17% !important;
}

.carousel-3d-slide.right-2,
.carousel-3d-slide.left-2,
.carousel-3d-slide.right-1,
.carousel-3d-slide.left-1 {
	border-radius: 6px;
}

.carousel-3d-slide.left-1 {
	transform: translateX(-440px) translateZ(-400px) rotateY(2deg) !important;
	height: 519px !important;
	top: -8% !important;
	width: 727px !important;
	left: -33% !important;
}

.carousel-3d-slide.left-2 {
	transform: translateX(-640px) translateZ(-600px) rotateY(2deg) !important;
	height: 486px !important;
	width: 727px !important;
	left: -47% !important;
}

.carousel-3d-slide.right-1 {
	transform: translateX(440px) translateZ(-400px) rotateY(-2deg) !important;
	top: -8% !important;
	height: 519px !important;
	right: -42% !important;
	width: 727px !important;
}

.carousel-3d-slide.right-2 {
	transform: translateX(640px) translateZ(-600px) rotateY(-2deg) !important;
	height: 486px !important;
	width: 727px !important;
	right: -51% !important;
}

.carousel-3d-slide {
	height: 400px !important;
	bottom: 20% !important;
	width: 427px !important;
}
</style>
