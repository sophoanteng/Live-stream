<!-- Usage
<pc-live-video :src="src"></pc-live-video> -->

<template>
        <view class="w-full h-full align-center" id="myPlayer">
        </view>
</template>
  
<script>
export default {
    name: "pc-live-video",
    data() {
        return {
            isPaused: false,
            liveinfo: {
                pull: "http://bo.dev.seokmcsowmsk.com/live/45553_1665567374.flv"
            },
            liveclass: [],
            uid: '',
            token: '',
            coin: '',
            liveuid: '',
            live_nicename: '',
            live_avatar: '',
            stream: '',
            anyway: '',
            userinfo: [],
            audience: [],
            chat_item: [],//聊天区域内容
            livechat: '',
            flag: true,
            flag_num: 1,
            gift_flag: true,
            gift_flag_num: 1,
            socket: '',
            scrollTop: 9999,
            modeClass: ['fade'],
            transfromClass: {
                position: 'absolute',
                bottom: 0,
                top: '120px',
                left: '10px',
                right: 0,
                display: 'flex',
                width: '220px',
                height: '100px',
                zIndex: 999999,
            },
            transShow: false,
            num: 0,
            transTime: '',
            chatid: '',
            //
            windowHeight: 800,
            windowWidth: 800,
            flvPlayer: null,
            player: null,
            liveCheckInterval: 0,
            videoInterval: 0,
            gift_list: [],
            currLiveGift: {},
        };
    },
    props: {
        src: {
            type: String,
            default() {
                return "";
            },
        },
    },
    mounted() {
        this.getLivePlayer();
    },
    methods: {
        getLivePlayer() {
            var player = document.createElement('video')
            player.enableProgressGesture = this.enableProgressGesture
            player.controls = true
            player.style.width= '100%'
            player.showCenterPlayBtn = this.showCenterPlayBtn
            player.showPlayBtn = this.showPlayBtn
            player.showFullscreenBtn = this.showFullscreenBtn
            player.x5VideoPlayerType = 'h5-page'
            player.x5VideoPlayerFullscreen = "false"
            player.autoplay = true   // 以上均为 video标签的属性配置
            document.getElementById("myPlayer").appendChild(player);
            if (flvjs.isSupported() && this.src.substr(-4) == ".flv") {
                this.flvPlayer = flvjs.createPlayer({
                    type: 'flv',
                    isLive: true, //<====直播的话，加个这个 
                    url: this.src,
                }, {
                    enableWorker: false, 			//不启用分离线程
                    enableStashBuffer: false, 		//关闭IO隐藏缓冲区
                    autoCleanupSourceBuffer: true 	//自动清除缓存
                });
                this.flvPlayer.attachMediaElement(player);
                this.flvPlayer.load(); //加载
                this.player = player;
                this.player.play()
                this.videoInterval = setInterval(() => {
                    if (this.player.buffered.length) {
                        let end = this.player.buffered.end(0);//获取当前时间值
                        let diff = end - this.player.currentTime;//获取相差差值
                        if (diff >= 1) {//如果差值大于等于0.5 手动跳帧 这里可根据自身需求来定
                            this.player.currentTime = this.player.buffered.end(0);//手动跳帧
                        }
                    }
                }, 5000);
            } else {
                player.src = this.src;
                player.play();
            }
        },
    },
};
</script>

<style>
    video {
        width: 100% !important;
    }
</style>
