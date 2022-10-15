<template>
  <div id="app" class="wrapper">
    <!-- <img class="chat-icon" @click="openFormUp" src="http://ecx.images-amazon.com/images/I/31%2BM902YK-L._SY300_.jpg" alt=""> -->
    <view class="model-chat">
      <!--v-if="showModal"-->
      <div class="modal-header">
        <h3>Lives Chat</h3>
      </div>
      <div class="box-gems">
        <div class="user">
          <img class="diemon" src="static/woman.png" alt="">
          <div class="text-user">
            <p style="color: white; font-size: 12px;">3,500 gems</p>
            <p style="color:white; font-size: 10px;">User696969</p>
          </div>
        </div>
        <button class="buy-gem">Buy Gems</button>
      </div>
      <div class="modal-body">
        <div class="box-comment" v-for="(item,index) in chat_item" :id="'chat'+index" :key="index">
          <img class="profile" src="static/woman.png" alt="">
          <div class="text-comment"><span>{{item.uname}}</span>{{item.content}}</div>
        </div>
      </div>
      <div class="box-comment-user">
        <img class="profile" src="static/woman.png" alt="">
        <input type="input" placeholder="Show your support..." v-bind:value="liveChat" @input="liveChatInput"
          @keydown.enter="$emit('sendMessage')" aria-label="send message" />
        <div class="icon-sticker"></div>
      </div>
      <div class="modal-footer">
        <img class="diemons" src="static/woman.png" alt="">
        <p style="color: gray; font-size: 10px; padding: 7px;">3,500 gems</p>
        <input class="type-text" type="text" />
        <button type="submit" class="send">Send</button>
      </div>
    </view>
  </div>
</template>
<script>
export default {
  name: 'pc-live-comment',
  data() {
    return {
      showModal: false,
    }
  },
  props: {
    chat_item: {
      type: Array,
      default: []
    },
    liveChat: {
      type: String,
      default: ""
    },
  },
  methods: {
    openFormUp() {
      this.showModal = !this.showModal
    },
    liveChatInput(e) {
      this.$emit('input', e)
    }
  }
}
</script>
<style scoped>
/* #app {
	font-family: Avenir, Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	text-align: center;
	color: #2c3e50;
} */
@keyframes example {
  75% {
    left: 0px;
    top: 200px;
  }

  100% {
    left: 0px;
    top: 0px;
  }
}

.modal-body {
  height: 430px;
  overflow-x: scroll;
}

.box-comment-user {
  display: flex;
  margin-left: 14px;
  margin-top: 4%;
  width: 366px;
  background: #F1F1F1;
  padding: 12px;
  border-radius: 6px;
}

.box-comment {
  display: flex;
  justify-content: start;
  align-items: flex-start;
  margin-left: 26px;
  margin-top: 16px;
  animation-name: example;
  animation-duration: 4s;
}

.text-comment span {
  font-size: 14px;
  margin-right: 5px;
  margin-top: 3px;
  font-weight: bold;
}

.text-comment {
  font-size: 13px;
  text-align: start;
  margin-left: 8px;
  margin-top: 3px;
}

.text-user {
  margin-left: 10px;
}

.type-text {
  margin-left: -13%;
  margin-top: 4px;
}

.modal-footer {
  display: flex;
  margin-top: 1%;
  position: fixed;
}

.diemons {
  width: 20px;
  height: 20px;
  margin-left: 25px;
  object-fit: cover;
  border-radius: 50px;
}

.diemon {
  width: 27px;
  height: 27px;
  object-fit: cover;
  border-radius: 50px;
}

.icon-sticker {
  width: 20px;
  height: 20px;
  background: rgb(189, 191, 189);
  border-radius: 50px;
  margin-left: 43%;
}

.profile {
  width: 20px;
  height: 20px;
  object-fit: cover;
  border-radius: 50px;
}

.user {
  display: flex;
  justify-content: center;
  align-items: center;
}

.send {
  width: 84px;
  font-size: 12px;
  left: 17%;
  top: -1px;
  background: linear-gradient(to right, #AF72FB 0%, #D472FB 100%);
  color: #ffff;
}

.buy-gem {
  width: 85px;
  font-size: 12px;
  left: 25%;
  top: 5px;
  background: white;
  color: #D472FB;
}

.box-gems {
  height: 55px;
  width: 373px;
  display: flex;
  justify-content: space-between;
  align-items: self-end;
  padding: 16px;
  justify-content: space-between;
  margin-left: 13px;
  border-radius: 6px;
  margin-top: 11px;
  background: linear-gradient(to right, #AF72FB 0%, #D472FB 100%);
}

.modal-header {
  border-bottom: 1px solid #80808063;
  padding: 3px 10px 18px;
}

.model-chat {
  background: white;
  padding: 16px 0px;
  border: none;
  cursor: pointer;
  opacity: 0.8;
  position: fixed;
  right: 0;
  width: 430px;
  height: 675px;
}

.chat-icon {
  width: 40px;
  height: 40px;
  border-radius: 50px;
  color: white;
  border: none;
  cursor: pointer;
  opacity: 0.8;
  position: fixed;
  margin-top: 36%;
  right: 28px;
}

.chat-popup {
  display: none;
  position: fixed;
  bottom: 0;
  right: 15px;
  border: 3px solid #f1f1f1;
  z-index: 9;
}

.form-container {
  max-width: 300px;
  padding: 10px;
  background-color: white;
}

.form-container textarea {
  width: 100%;
  padding: 15px;
  margin: 5px 0 22px 0;
  border: none;
  background: #f1f1f1;
  resize: none;
  min-height: 200px;
}

.form-container textarea:focus {
  background-color: #ddd;
  outline: none;
}

.form-container .btn {
  background-color: #04AA6D;
  color: white;
  padding: 16px 20px;
  border: none;
  cursor: pointer;
  width: 100%;
  margin-bottom: 10px;
  opacity: 0.8;
}

.form-container .cancel {
  background-color: red;
}

.form-container .btn:hover,
.open-button:hover {
  opacity: 1;
}
</style>
