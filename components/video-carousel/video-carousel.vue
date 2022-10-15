<template>
    <div class="carousel">
      <div @click="changeVideo('right')" class="cursor-pointer">
        <i class="fa-solid fa-chevron-left text-xs"></i>
      </div>
      <div class="carousel__content">
        <div class="carousel__video"
          v-for="(item, i) in videos"
          :class="item.position"
          :key="i"
        >
        <div class="carousel__overlay" @click="changeVideo(0, item.position)" @dblclick="openUrl(`/pages/pc/live/index?liveuid=${item.uid}&stream=${item.stream}&anyway=${item.anyway}&live_nicename=${item.user_nicename}&live_avatar=${item.avatar_thumb}&flv=${item.pull}`)"/>
        <video 
          v-if="!item.active"
          :src="item.embed" 
          alt=""
          class="carousel__video-picture"
          :autoplay="item.position === 'main' ? true : false"
        />
      </div>
      </div>
      <div @click="changeVideo('left')" class="cursor-pointer">
        <i class="fa-solid fa-chevron-right text-xs"></i>
      </div>
  </div>
</template>
<script>
import video from '../../pages/video/video.vue';
  export default {
  components: { video },
    name: 'video-carousel',
    data() {
      return {
        currentVideo: 0,
        // totalVideos: this.videos.length,
        videoPositions: {
          0 : "tertiary tertiary-left",
          1 : "secondary secondary-left",
          2 : "main",
          3 : "secondary secondary-right",
          4 : "tertiary tertiary-right"
        }
      }
    },
    props: ['videos'],
    methods: {
      openUrl(url) {
				console.log('url',url)
				uni.$emit("close_socket");
				uni.navigateTo({
					url: url
				});
			},
		  changeVideo: function(direction, position) {
        console.log('direction, position', direction, position);
        if(direction == "left") {
          this.currentVideo--;
          if(this.currentVideo < 0) {
            this.currentVideo = 4;
          }
          
        } else if (direction == "right") {
          this.currentVideo++;
          if(this.currentVideo > 4) {
            this.currentVideo = 0;
          }
        } else {
          switch(position) {
            case 'secondary secondary-right':
              this.changeVideo('left');
              break;
            case 'secondary secondary-left':
              this.changeVideo('right');
              break;
            case 'tertiary tertiary-right':
              this.changeVideo('left');
              this.changeVideo('left');
              break;
            case 'tertiary tertiary-left':
              this.changeVideo('right');
              this.changeVideo('right');
              break;
          }
        }
        
        this.videos.forEach((video, i)=>{
          video.active = false;
          let newIndex = i+this.currentVideo;
          if(newIndex > 4) {
            newIndex %= 5;
          }
          video.position = this.videoPositions[newIndex];
        });
        
      }
    },
  }
</script>
<style>
  @import url("@/common/pc/video-carousel.css");
</style>
