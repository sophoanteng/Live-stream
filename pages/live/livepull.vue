<template>
	
	<view class="content">
		<!-- 播放器 -->
		<view class="live_pull" >
			<view class="pull">
				<view class="uni-video-container" id="myPlayer" @click="transport_g()">
					
				</view>
			</view>
		</view>
		<!-- <view class="live_pull">
			<video :src="liveinfo.pull" class="pull" controls="false" autoplay="true" loop="true" show-play-btn="false" show-center-play-btn="false" muted="false" :controls="false" @click="transport_g()"></video>
		</view> -->
		<!-- 主播和观众席信息 -->
		<livepull-top class="live_user" :liveuid="liveuid" :live_nicename="live_nicename" :live_avatar="live_avatar" :audience="audience"></livepull-top>
		
		<uni-transition :duration="500" :mode-class="modeClass" :styles="transfromClass" :show="transShow">
			<view class="ugift">
				<!-- 用户头像 -->
				<view class="thumd">
					<image :src="live_avatar" class="thumd_t" mode=""></image>
				</view>
				
				<!-- 礼物信息-->
				<view class="info">
					<view class="uname">
						<text class="uname_text">{{live_nicename}}</text>
					</view>
					<view class="gift_info">
						<text class="gift_text">送</text>
						<text class="gift_name">{{currLiveGift.giftname}}</text>
					</view>
					<!-- 礼物图标 -->
					<view class="gift_thumd">
						<image :src="currLiveGift.gifticon" class="thumd_t" mode=""></image><text class="num">x{{num}}</text>
					</view>
				</view>
				
				
			</view>
			
		</uni-transition>
		
		<!-- 公屏聊天处 -->
		<livepull-chat :flag="flag" :livechat="livechat" :live_avatar="live_avatar" :chat_item="chat_item" :chatid="chatid" @input="livechat_input"  @click="send()"  @transport="transport()"  @goback="goback()" @gift_icon="gift_icon()">
		</livepull-chat>
		
		<!-- 礼物列表 -->
		<live-gift :gift_flag="gift_flag" :list="gift_list" :curr_gift="currLiveGift" :coin="coin" @gift_give="gift_give()" @gift_change="gift_change"  @open="open(['slide-left'])"></live-gift>
		
		
	</view>
</template>

<script>
	import livepullTop from "./components/livepulltop.vue";
	import livepullChat from "./components/livepullchat.vue";
	import liveGift from "./components/livegift.vue";
	import io from '../../js_sdk/hyoga-uni-socket_io/uni-socket.io.js';
	
	const app = getApp();
	export default {
		data() {
			return {
				uid:'',
				token:'',
				coin:'',
				liveuid:'',
				live_nicename:'',
				live_avatar:'',
				stream:'',
				anyway:'',
				liveinfo:[],
				userinfo:[],
				audience:[],
				chat_item: [],//聊天区域内容
				livechat:'',
				flag:true,
				flag_num:1,
				gift_flag:true,
				gift_flag_num:1,
				socket:'',
				scrollTop:9999,
				modeClass: ['fade'],
				transfromClass: {
					position: 'fixed',
					bottom: 0,
					top: '150px',
					left: '10px',
					right: 0,
					display: 'flex',
					width:'220px',
					height:'100px'
				},
				transShow: false,
				num:0,
				transTime:'',
				chatid:'',
				
				//
				windowHeight: '',
				flvPlayer: null,
				player: null,
				videoInterval:0,
				liveCheckInterval:0,
				gift_list:[],
				currLiveGift:{},
			}
		},
		onLoad(e) {
			let that = this;
			this.videoContext = uni.createVideoContext('pull',this);
			this.videoContext.requestFullScreen();
			//判断是否登入
			var res = global.isLogin();
			if(!res){
				uni.showModal({
					title:'请登录',
					content:"请登录",
					showCancel:false,
					success:function(){
						if(that.liveCheckInterval){
							clearInterval(that.liveCheckInterval);
						}
						uni.navigateTo({
							url:'../login/login'
						});
					}
				})
			}
			this.uid=res[0];
			this.token=res[1];
			console.log("token:"+this.token+"，uid:"+this.uid);
			this.liveuid=e.liveuid;
			this.stream=e.stream;
			this.anyway=e.anyway;
			this.live_nicename=e.live_nicename;
			this.live_avatar=e.live_avatar;
			
			let sysInfo = uni.getSystemInfoSync()
			this.windowWidth = sysInfo.screenWidth
			this.windowHeight = sysInfo.windowHeight
			
			uni.request({
				url: app.globalData.site_url +'/appapi/?service=User.getUserHome',
				method: 'GET',
				data: {
					uid:this.uid,
					token:this.token,
					touid:this.uid,
				},
				success: res => {
					var data=res.data.data;;
					if(data.code!=0){
						uni.showToast({
							title: data.msg,
							icon:'none',
							duration: 2000
						});
						return 1;
					}
					this.userinfo=data['info'][0];
					
				},
				fail: () => {},
				complete: () => {}
			});
			uni.request({
				url: app.globalData.site_url +'/appapi/?service=Live.enterRoom',
				method: 'GET',
				data: {
					uid:this.uid,
					token:this.token,
					liveuid:this.liveuid,
					stream:this.stream,
				},
				success: res => {
					var data=res.data.data;;
					if(data.code!=0){
						uni.showToast({
							title: data.msg,
							icon:'none',
							duration: 2000
						});
						return 1;
					}
					this.liveinfo=data['info'][0];
					this.audience=data['info'][0]['userlists'];
					this.coin=data['info'][0]['coin'];
					
					this.getLivePlayer();
					
					console.log(this.liveinfo);
					//调用链接socket
					this.socketConnect(this.chat_item);
				},
				fail: () => {},
				complete: () => {}
			});
			this.getGiftListData();
		},
		mounted() {
			// #ifdef H5
		
			// #endif
			// this.$nextTick(() => {
			// 	this.getLivePlayer()
			// })
			//console.log(flvjs)
			let that = this;
			this.$once('hook:beforeDestroy', () => {
				if(that.videoInterval){
					clearInterval(that.videoInterval);
				}
				if(that.liveCheckInterval){
					clearInterval(that.liveCheckInterval);
				}
				if(that.socket){
					that.socket.close(); //关闭socket
				}
			});
			
			this.liveCheckInterval = setInterval(() => {
				that.liveCheck();
			}, 5000); //5000毫秒执行一次 
			
			//关闭聊天窗口
			uni.$on("transport_g",e=>{
				console.log(e);
				this.transport_g();
			});
		},
		methods: {
			gift_change(e){
				console.log(e);
				if(e.id != this.currLiveGift.id){
					this.num = 0;
				}
				this.currLiveGift = e;
			},
			goback(){
				//退出房间
				let pages = getCurrentPages(); // 当前页面
				let beforePage = pages[pages.length - 2]; // 前一个页面
				uni.navigateBack({
				 success: function() {
					if(this.socket){
						this.socket.close(); //关闭socket
					}
					beforePage.onLoad(); // 执行前一个页面的onLoad方法
				 }
				});
			},
			liveCheck(){
				uni.request({
					url: app.globalData.site_url +'/appapi/?service=Live.CheckLive',
					header: { 'content-type': 'application/x-www-form-urlencoded' },
					method: 'post',
					data: {
						uid:this.uid,
						token:this.token,
						liveuid:this.liveuid,
						stream:this.stream
					},
					success: res => {
						console.log(res);
						var data=res.data.data;
						if(data.code && data.code!=0){
							this.liveEnd();
						}
					},
					fail: () => {},
					complete: () => {}
				});
			},
			liveEnd(){
				clearInterval(this.liveCheckInterval);
				uni.showModal({
					title:"提示",
					content:"直播已关闭！",
					showCancel:false,
					success: function (res) {
						uni.redirectTo({
							url:"/pages/index/index"
						})
					}
				})
			},
			getLivePlayer() {  // 生成需要的video 组件
				//return;
				// var player = document.getElementById('videoElement');
				var player = document.createElement('video')
				player.id = 'video'
				player.className='pull'
				player.style = 'width:' + this.windowWidth + 'px;height: '+ this.windowHeight + 'px'
				player.enableProgressGesture = this.enableProgressGesture
				player.controls=this.controls
				player.showCenterPlayBtn=this.showCenterPlayBtn
				player.showPlayBtn=this.showPlayBtn
				player.showFullscreenBtn=this.showFullscreenBtn
				player.x5VideoPlayerType='h5-page'
				player.x5VideoPlayerFullscreen="false" 
				//player.src="http://live1.vcqbbs.com/16666.mp4"
				player.autoplay=this.autoplay   // 以上均为 video标签的属性配置
				document.getElementById("myPlayer").appendChild(player);
				console.log(player)
				console.log(flvjs.isSupported())
				if (flvjs.isSupported() && this.liveinfo.pull.substr(-4) == ".flv") {
					this.flvPlayer = flvjs.createPlayer({
						type: 'flv',
						isLive: true, //<====直播的话，加个这个 
						url: this.liveinfo.pull,//'http://livepull.milang.net/live/45585_1659876975.flv', //<==自行修改
					},{
						enableWorker: false, 			//不启用分离线程
						enableStashBuffer: false, 		//关闭IO隐藏缓冲区
						autoCleanupSourceBuffer: true 	//自动清除缓存
					});
					console.log(this.flvPlayer)
					this.flvPlayer.attachMediaElement(player);
					this.flvPlayer.load(); //加载
					player.play()
					this.player = player;
					console.log("isSupported")
					//this.flv_start();
					// uni.showModal({
					// 	cancelText:"1111"
					// })
					
					this.videoInterval = setInterval(() => {
						if (this.player.buffered.length) {
						let end = this.player.buffered.end(0);//获取当前时间值
						let diff = end - this.player.currentTime;//获取相差差值
						if (diff >= 1) {//如果差值大于等于0.5 手动跳帧 这里可根据自身需求来定
							this.player.currentTime = this.player.buffered.end(0);//手动跳帧
						}
					  }
					}, 5000); //5000毫秒执行一次 
				}else{
					player.src=this.liveinfo.pull;
					player.play();
					// uni.showModal({
					// 	cancelText:"222"
					// })
				}
			},
			//************连接socket，接受socket
			socketConnect(chat_item) {
				var liveinfo=this.liveinfo;
				var data = {
				  uid: this.uid,
				  username: this.userinfo.user_nicename,
				  token: this.token,
				  roomnum: this.liveuid,
				  stream: this.stream,
				};
				//socket链接
				this.socket=io(app.globalData.socket_url);
				
				this.socket.on('connect', function (datas) {
					console.log('连接socket成功');
					socket.emit("conn", data);
				});		
				const socket = this.socket;			
				/*客户端广播接收broadcasting*/
				socket.on('broadcastingListen', function (data) {
				    for(var i=0;i<data.length;i++){
						if(i!='remove'){
							if(data[i]=="stopplay"){
								JsInterface.superStopRoom();
							}else{
								var data = JSON.parse(data[i]);
								var msgObject = data.msg[0];
								var msgtype = msgObject.msgtype;
								var msgaction = msgObject.action;
								var msgmethod = msgObject._method_;
								var retcode = data.retcode;
								if(retcode=='409002'){
								    uni.showToast({
								    	title: '你已被禁言',
								    	icon:'none',
								    	duration: 2000
								    });
								    return;
								}
								console.log('聊天信息：');
								console.log(msgObject);
								if(msgmethod=='SendMsg'){ //聊天信息
									if(msgtype==0){  //用户进入
										//接受普通聊天消息   点亮RGB(157, 201, 255) ran
										var ct = msgObject["ct"];
										var uname = ct['user_nicename'];
										var uavatar = ct['avatar_thumb'];
										var uid = ct['id'];
										var content = "进入了直播间";
										var vip_type=ct['vip_type'];
										var level=ct['level'];
										var dic = {
											uid: uid,
											uname: uname,
											uavatar: uavatar,
											msgtype: msgtype,
											content: content,
											method: msgmethod, //根据method判断字体颜色
											vip_type:vip_type,
										};
										chat_item.push(dic);
									}else if(msgtype == '1'){ //直播结束
										uni.showToast({
											title: '直播结束',
											icon:'none',
											duration: 2000
										});
									}else if(msgtype == '2'){ //普通聊天
										//接受普通聊天消息
										var ct = msgObject["ct"];
										var uid = msgObject['uid'];
										var uname = msgObject['uname'];
										var uavatar = msgObject['avatar_thumb'];
										var level = msgObject['level'];
										var vip_type = msgObject['vip_type'];
										var liangname = msgObject['liangname'];
										var level=msgObject['level'];
										//判断点亮
										var dic = {
											msgtype: msgtype,
											light: msgObject['heart'],//判断是不是点亮消息
											vip_type: vip_type,//判断是不是vip
											liangname: liangname,//判断是不是靓号
											uname: uname,//用户名称
											uid: uid,//用户名称
											content: ct,//聊天内容 
											method: msgmethod,//根据method判断字体颜色
										};
										chat_item.push(dic);
										console.log(chat_item);
									}
									
									
								}else if(msgmethod=='SendGift'){ //赠送礼物
									//接受普通聊天消息
									var ct = msgObject["ct"];
									var uid = msgObject['uid'];
									var uname = msgObject['uname'];
									var uavatar = msgObject['avatar_thumb'];
									var content = "送了1个"+ct['giftname'];	
									//判断点亮
									var dic = {
										uname: uname,//用户名称
										uid: uid,//用户名称
										content: content,//聊天内容 
										method: msgmethod,//根据method判断字体颜色
									};
									chat_item.push(dic);
									
									

									
								}else if(msgmethod=='BuyKeeper'){ //购买守护
									//this.buyKeeper(msgObject);
								}else if(msgmethod=='SendHorn'){ //喇叭
									this.sendHorn(msgObject);
								}else if(msgmethod=='SystemNot' || msgmethod=='ShutUpUser'){ 
									//系统信息
									var dic = {
									  content: msgObject.ct,//聊天内容 
									  method: 'SystemNot',//根据method判断字体颜色
									};
									chat_item.push(dic);
								}else if(msgmethod=='StartEndLive'){ //开关播
									//this.showEndRecommend(msgObject);
									this.liveCheck();
								}else if(msgmethod=='disconnect'){ //关播
									//this.disconnect(msgObject);
									this.liveCheck();
								}else if(msgmethod=='requestFans'){ //关播
									//var nums=msgObject.ct.data.info.nums;
									//this.setRoomNums(nums);
								}else if(msgmethod=='KickUser'){ //踢人
									this.KickUser(msgObject);
								}
							}			
						}
				
					} 
				});
				socket.on('disconnect', function () {
					console.log('you have been disconnected');
				});
			},
			send() {
				var that = this;
				if (that.livechat == "") {
					uni.showToast({
						icon: 'none',
						title: '请输入内容'
					});
					return;
				}
				
				var userinfo=this.userinfo;
				
				var data = {
				  msg: [
				    {
				      _method_: 'SendMsg',
				      action: '0',
				      msgtype: '2',
				      ct: that.livechat,
				      uid: userinfo.id,
				      uname: userinfo.user_nicename,
				      roomnum: this.liveuid,
				      level: userinfo.level,
				      vip_type: userinfo.vip.type,
				      liangname: userinfo.liang.name,
				    }],
				  'retcode': '000000',
				  'retmsg': 'OK'
				};
				that.socket.emit('broadcast', data);
				that.livechat='';
				
				//公屏聊天滚动条滚到底部
				this.chat_list();
			},
			gift_give(){
				var isback=0;
				var giftype=0;
				var giftnums=1;

				var is_sticker=0;

				var that = this;
				var userinfo=that.userinfo;
				var liveinfo=that.liveinfo;
				
				uni.request({
					url: app.globalData.site_url +'/appapi/?service=Live.sendGift',
					header: { 'content-type': 'application/x-www-form-urlencoded' },
					method: 'post',
					data: {
						uid:this.uid,
						token:this.token,
						liveuid:this.liveuid,
						stream:this.stream,
						giftcount: giftnums,
						giftid: this.currLiveGift.id,
						ispack:isback,
						is_sticker:is_sticker,
						touids:this.liveuid
					},
					success: res => {
						var data=res.data.data;
						if(data.code!=0){
							uni.showToast({
								title: data.msg,
								icon:'none',
								duration: 2000
							});
							return 1;
						}
						this.open(['slide-left']);
						this.coin=data['info'][0]['coin'];						
						var data = {
							msg: [{
								_method_: 'SendGift',
								action: '0',
								msgtype: '1',
								roomnum: this.liveuid,
								ct: data['info'][0]['gifttoken'],
								livename:this.live_nicename,
								level:data['info'][0]['level'],
								uid: userinfo.id,
								uname: userinfo.user_nicename,
								uhead: userinfo.avatar,
								vip_type: userinfo.vip.type,
								liangname: userinfo.liang.name,
								usertype:liveinfo.usertype,
								guard_type:0
							}],
							'retcode': '000000',
							'retmsg': 'OK'
						};						
						that.socket.emit('broadcast', data);

					},
					fail: () => {},
					complete: () => {}
				});
			},
			transport(){
				//展示输入框
				this.flag=false;
				this.flag_num=2;
			},
			transport_g(){
				//隐藏输入框
				if(this.flag_num==2){
					this.flag=true;
					this.flag_num=1;
				}
				
				//隐藏礼物列表
				if(this.gift_flag_num==2){
					this.gift_flag=true;
					this.gift_flag_num=1;
				}
			},
			livechat_input(e){
				//聊天发送内容
				this.livechat=e.detail.value;
			},
			gift_icon(){
				//展示输入框
				this.gift_flag=false;
				this.gift_flag_num=2;
			},
			open(mode) {
				clearTimeout(this.transTime);
				this.modeClass = mode;
				this.transShow = true;
				this.num=this.num+1;
				
				this.transTime=setTimeout(() => {
					this.transShow = false;
					this.num=0;
					
				}, 3000);
				//公屏聊天滚动条滚到底部
				this.chat_list();
			},
			chat_list(){
				setTimeout(() => {
				//公屏聊天滚动条滚到底部
				let len = this.chat_item.length;
				this.chatid = 'chat'+(len-1); 
				}, 500);
			},
			//获取礼物列表
			getGiftListData(){
				var that = this;
				var userinfo=that.userinfo;
				
				uni.request({
					url: app.globalData.site_url +'/appapi/?service=Live.GetGiftList',
					header: { 'content-type': 'application/x-www-form-urlencoded' },
					method: 'post',
					data: {
						uid:this.uid,
						token:this.token,
						live_type:0,
					},
					success: res => {
						console.log(res);
						var data=res.data.data;
						if(data.code!=0){
							uni.showToast({
								title: data.msg,
								icon:'none',
								duration: 2000
							});
							return 1;
						}
						this.gift_list = data.info[0].giftlist;
						this.currLiveGift = this.gift_list[0];
						console.log(this.currLiveGift);
					},
					fail: () => {},
					complete: () => {}
				});
			}
		
		},
		components:{
			livepullTop,
			livepullChat,
			liveGift
		}
	}
</script>

<style>
	.content{
		width: 100vw;
		height: 100vh;
		/* background-color: #000000; */
		position: relative;
		overflow: hidden;
	}
	.content .live_pull .pull{
		width: 100vw;
		height: 100vh;
	}
	.ugift{
		width: 240px;
		/* #ifdef H5 */
		height:50px;
		background-color: rgba(0 0 0 / 0.3);
		/* #endif */
		
		/* #ifdef APP-NVUE */
		height:52px;
		background-color: #000000;
		/* #endif */
		border-radius: 40px;
		padding: 2px;
		position: relative;
	}
	.ugift  .thumd{
		/* #ifdef H5 */
		width: 50px;
		height:50px;
		/* #endif */
		
		/* #ifdef APP-NVUE */
		width: 48px;
		height:48px;
		/* #endif */
		
		position: relative;
	}
	.ugift .thumd .thumd_t{
		/* #ifdef H5 */
		width: 50px;
		height:50px;
		border-radius: 50px;
		/* #endif */
		
		/* #ifdef APP-NVUE */
		width: 48px;
		height:48px;
		border-radius: 48px;
		/* #endif */
		
		position: absolute;
	}
	.ugift .info{
		position: absolute;
		top:0;
		left: 50px;
		width: 140px;
		
	}
	.ugift .info .uname{
		height: 25px;
		line-height: 25px;
	}
	.ugift .info .uname .uname_text{
		width: 80px;
		color: RGB(157, 201, 255);
		font-size: 14px;
		overflow: hidden;
	}
	.ugift .info .gift_info{
		height: 25px;
		line-height: 25px;
	}
	.ugift .info .gift_info .gift_text{
		font-size: 14px;
		color: #FFFFFF;
	}
	
	.ugift .info .gift_info .gift_name{
		font-size: 16px;
		color: #fbb06e;
	}
	.ugift  .gift_thumd{
		/* #ifdef H5 */
		width: 45px;
		height:45px;
		/* #endif */
		
		/* #ifdef APP-NVUE */
		width: 43px;
		height:43px;
		/* #endif */
		
		position: absolute;
		top: 0;
		right: 15px;
	}
	.ugift .gift_thumd .thumd_t{
		/* #ifdef H5 */
		width: 45px;
		height:45px;
		border-radius: 45px;
		/* #endif */
		
		/* #ifdef APP-NVUE */
		width: 43px;
		height:43px;
		border-radius: 43px;
		/* #endif */
	}
	.ugift .gift_thumd .num{
		position: absolute;
		top: 5px;
		font-size: 28px;
		color: #fff;
	}

		
	
	
	
	
	
</style>
