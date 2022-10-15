<template>
	<view>
		<pc-topbar />
		<view class="live-container add-on">
			<pc-live-comment :chat_item="chat_item" @input="liveChatInput" @sendMessage="sendMessage"
				:liveChat="liveChat" />
			<view class="w-[95%] rounded-b-[6px]">
				<pc-live-video :src="flv" />
			</view>
		</view>
		<view class="live-container">
			<pc-group-thumbnail :items="items" title="Hot Channel" />
			<pc-group-thumbnail :items="items" title="Watched Channel" />
		</view>
	</view>
</template>

<script>
import io from '@/js_sdk/hyoga-uni-socket_io/uni-socket.io.js';
const app = getApp();
export default {
	data() {
		return {
			items: [],
			flv: "",
			// liveclass: [],
			liveclass: [],
			uid: '',
			token: '',
			coin: '',
			liveuid: '',
			live_nicename: '',
			live_avatar: '',
			stream: '',
			anyway: '',
			liveinfo: [],
			userinfo: [],
			audience: [],
			chat_item: [],//聊天区域内容
			liveChat: '',
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
		}
	},
	onLoad(e) {
		this.flv = e.flv;
		uni.request({
			url: app.globalData.site_url + '/appapi/?service=Home.gethot',
			method: 'GET',
			data: {
				p: 1
			},
			success: res => {
				this.slide = res.data.data.info[0].slide;
				this.items = res.data.data.info[0].list;
			},
			fail: () => { },
			complete: () => { }
		});
		let that = this;
		var res = global.isLogin();
		if (!res) {
			uni.showModal({
				title: '请登录',
				content: "请登录",
				showCancel: false,
				success: function () {
					if (that.liveCheckInterval) {
						clearInterval(that.liveCheckInterval);
					}
					uni.navigateTo({
						url: '../login/login'
					});
				}
			})
		}
		this.uid = res[0];
		this.token = res[1];

		this.liveuid = e.liveuid;
		this.stream = e.stream;
		this.anyway = e.anyway;
		this.live_nicename = e.live_nicename;
		this.live_avatar = e.live_avatar;

		uni.request({
			url: app.globalData.site_url + '/appapi/?service=User.getUserHome',
			method: 'GET',
			data: {
				uid: this.uid,
				token: this.token,
				touid: this.uid,
			},
			success: res => {
				var data = res.data.data;;
				if (data.code != 0) {
					uni.showToast({
						title: data.msg,
						icon: 'none',
						duration: 2000
					});
					return 1;
				}
				this.userinfo = data['info'][0];

			},
			fail: () => { },
			complete: () => { }
		});
		uni.request({
			url: app.globalData.site_url + '/appapi/?service=Live.enterRoom',
			method: 'GET',
			data: {
				uid: this.uid,
				token: this.token,
				liveuid: this.liveuid,
				stream: this.stream,
			},
			success: res => {
				var data = res.data.data;;
				if (data.code != 0) {
					uni.showToast({
						title: data.msg,
						icon: 'none',
						duration: 2000
					});
					return 1;
				}
				this.liveinfo = data['info'][0];
				this.audience = data['info'][0]['userlists'];
				this.coin = data['info'][0]['coin'];
				//调用链接socket
				this.socketConnect(this.chat_item);
			},
			fail: () => { },
			complete: () => { }
		});

		uni.$on("close_socket", e => {
			if (this.socket) {
				this.socket.close(); //关闭socket
			}
		});

		// this.$once('hook:beforeDestroy', () => {
		// 		if(that.liveCheckInterval){
		// 			clearInterval(that.liveCheckInterval);
		// 		}
		// 		if(that.socket){
		// 			that.socket.close(); //关闭socket
		// 		}
		// 	});

		this.liveCheckInterval = setInterval(() => {
			this.liveCheck();
		}, 5000);
	},
	methods: {
		sendMessage() {
			if (this.liveChat == "") {
				uni.showToast({
					icon: 'none',
					title: '请输入内容'
				});
				return;
			}

			var userinfo = this.userinfo;

			var data = {
				msg: [
					{
						_method_: 'SendMsg',
						action: '0',
						msgtype: '2',
						ct: this.liveChat,
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
			this.socket.emit('broadcast', data);
			this.liveChat = '';

			//公屏聊天滚动条滚到底部
			this.chat_list();
		},
		chat_list() {
			setTimeout(() => {
				//公屏聊天滚动条滚到底部
				let len = this.chat_item.length;
				this.chatid = 'chat' + (len - 1);
			}, 500);
		},
		liveChatInput(e) {
			//聊天发送内容
			this.liveChat = e.detail.value;
		},
		socketConnect(chat_item) {
			var that = this;
			var liveinfo = this.liveinfo;
			var data = {
				uid: this.uid,
				username: this.userinfo.user_nicename,
				token: this.token,
				roomnum: this.liveuid,
				stream: this.stream,
			};
			this.socket = io(app.globalData.socket_url);

			this.socket.on('connect', function (datas) {
				socket.emit("conn", data);
			});
			const socket = this.socket;
			/*客户端广播接收broadcasting*/
			socket.on('broadcastingListen', function (data) {
				for (var i = 0; i < data.length; i++) {
					if (i != 'remove') {
						if (data[i] == "stopplay") {
							JsInterface.superStopRoom();
						} else {
							var data = JSON.parse(data[i]);
							var msgObject = data.msg[0];
							var msgtype = msgObject.msgtype;
							var msgaction = msgObject.action;
							var msgmethod = msgObject._method_;
							var retcode = data.retcode;
							if (retcode == '409002') {
								uni.showToast({
									title: '你已被禁言',
									icon: 'none',
									duration: 2000
								});
								return;
							}

							if (msgmethod == 'SendMsg') { //聊天信息
								if (msgtype == 0) {  //用户进入
									//接受普通聊天消息   点亮RGB(157, 201, 255) ran
									var ct = msgObject["ct"];
									var uname = ct['user_nicename'];
									var uavatar = ct['avatar_thumb'];
									var uid = ct['id'];
									var content = "进入了直播间";
									var vip_type = ct['vip_type'];
									var level = ct['level'];
									var dic = {
										uid: uid,
										uname: uname,
										uavatar: uavatar,
										msgtype: msgtype,
										content: content,
										method: msgmethod, //根据method判断字体颜色
										vip_type: vip_type,
									};
									chat_item.push(dic);
								} else if (msgtype == '1') { //直播结束
									uni.showToast({
										title: '直播结束',
										icon: 'none',
										duration: 2000
									});
								} else if (msgtype == '2') { //普通聊天
									//接受普通聊天消息
									var ct = msgObject["ct"];
									var uid = msgObject['uid'];
									var uname = msgObject['uname'];
									var uavatar = msgObject['avatar_thumb'];
									var level = msgObject['level'];
									var vip_type = msgObject['vip_type'];
									var liangname = msgObject['liangname'];
									var level = msgObject['level'];
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
								}


							} else if (msgmethod == 'SendGift') { //赠送礼物
								//接受普通聊天消息
								var ct = msgObject["ct"];
								var uid = msgObject['uid'];
								var uname = msgObject['uname'];
								var uavatar = msgObject['avatar_thumb'];
								var content = "送了1个" + ct['giftname'];
								//判断点亮
								var dic = {
									uname: uname,//用户名称
									uid: uid,//用户名称
									content: content,//聊天内容 
									method: msgmethod,//根据method判断字体颜色
								};
								chat_item.push(dic);




							} else if (msgmethod == 'BuyKeeper') { //购买守护
								//this.buyKeeper(msgObject);
							} else if (msgmethod == 'SendHorn') { //喇叭
								this.sendHorn(msgObject);
							} else if (msgmethod == 'SystemNot' || msgmethod == 'ShutUpUser') {
								//系统信息
								var dic = {
									content: msgObject.ct,//聊天内容 
									method: 'SystemNot',//根据method判断字体颜色
								};
								chat_item.push(dic);
							} else if (msgmethod == 'StartEndLive') { //开关播
								//this.showEndRecommend(msgObject);
								that.liveCheck();
							} else if (msgmethod == 'disconnect') { //关播
								//this.disconnect(msgObject);
								that.liveCheck();
							} else if (msgmethod == 'requestFans') { //关播
								//var nums=msgObject.ct.data.info.nums;
								//this.setRoomNums(nums);
							} else if (msgmethod == 'KickUser') { //踢人
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
		liveCheck() {
			uni.request({
				url: app.globalData.site_url + '/appapi/?service=Live.CheckLive',
				header: { 'content-type': 'application/x-www-form-urlencoded' },
				method: 'post',
				data: {
					uid: this.uid,
					token: this.token,
					liveuid: this.liveuid,
					stream: this.stream
				},
				success: res => {
					var data = res.data.data;
					if (data.code && data.code != 0) {
						// this.liveEnd();
					}
				},
				fail: () => { },
				complete: () => { }
			});
		},
		liveEnd() {
			clearInterval(this.liveCheckInterval);
			uni.showModal({
				title: "提示",
				content: "直播已关闭！",
				showCancel: false,
				success: function (res) {
					uni.redirectTo({
						url: "/pages/pc/index/index"
					})
				}
			})
		},
	},
}
</script>

<style>
.live-container {
	position: relative;
	width: calc(100vw - 430px);
	margin-right: 430px;
	/* padding-top: 92px; */
	/* padding-bottom: 3rem; */
	display: flex;
	justify-content: center;
	align-content: flex-start;
	flex-wrap: wrap;
}

.add-on {
	padding-top: 92px;
	padding-bottom: 1rem;
}
</style>
