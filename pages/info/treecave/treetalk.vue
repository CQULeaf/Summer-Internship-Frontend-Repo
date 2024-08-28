<template>
	<view class="container">
			<u-navbar class="wrap" height=60 title="溯游从之" title-size=40 :background="background" is-back=true
				:customBack="gotopofile">
			</u-navbar>
		<view class="chat-box" ref="chatBox">
			<view class="message" v-for="(msg, index) in messages" :key="index">
				{{ msg }}
			</view>
		</view>
		<view class="input-container">
			<input v-model="messageInput" placeholder="编辑你的内容..." />
			<button @click="sendMessage">发送</button>
		</view>
	</view>
</template>


<script>
import { Stomp } from '../../../js/stomp.js';
export default {
    data() {
        return {
            background: {
                backgroundColor: '#001f3f',
                backgroundSize: 'cover',
                backgroundImage: 'linear-gradient(45deg, rgb(159, 219, 196),rgb(139, 219, 186),rgb(35, 187, 154))'
            },
            stompClient: null,
            messages: [],
            messageInput: '',
            currentUserId: '',
            receiverId: '', // 用于存储聊天对象的ID
            currentnickname: '',
            receivernickname: '',
            currentavatar: ''
        };
    },

    onReady() {
        this.initWebSocket(); // 在页面加载完成时，初始化WebSocket连接
    },

    methods: {
        initWebSocket() {
            const socket = new WebSocket('ws://127.0.0.1:8080/endpoint-websocket');
            this.stompClient = Stomp.over(socket);

            this.stompClient.connect({}, frame => {
                console.log('STOMP连接成功:', frame);
                this.subscribePrivateChat(); // 连接成功后，立即订阅私人聊天频道
            }, error => {
                console.error('STOMP连接错误:', error);
                this.reconnectWebSocket(); // 处理连接错误
            });
        },

        subscribePrivateChat() {
            // 订阅私人聊天消息
            this.stompClient.subscribe('/user/' + this.currentUserId + '/private', message => {
                console.log('收到消息:', message.body);
                this.messages.push(JSON.parse(message.body));
                this.$nextTick(() => {
                    const chatBox = this.$refs.chatBox;
                    chatBox.scrollTop = chatBox.scrollHeight;
                });
            });
        },

        sendMessage() {
            if (this.messageInput.trim() !== '') {
                let message = {
                    senderId: this.currentUserId,
                    receiverId: this.receiverId,
                    content: this.messageInput,
                    createdAt: new Date()
                };
                console.log('发送的消息:', message);
                this.messages.push(message);
                this.stompClient.send('/app/private', {}, JSON.stringify(message));
                this.messageInput = ''; // 清空输入框
            }
        },

        reconnectWebSocket() {
            if (!this.stompClient.connected) {
                console.log('尝试重新连接WebSocket');
                this.initWebSocket();
            }
        },

        gotopofile() {
            uni.switchTab({
                url: "/pages/info/infopage"
            });
        },
    },

    mounted() {
        // 从路由参数获取聊天对象的ID
        this.receiverId = this.$route.query.userId;
        console.log('获取到的 userId:', this.receiverId);

        // 获取当前用户信息
        uni.getStorage({
            key: 'nowAccount',
            success: (res) => {
                this.currentUserId = res.data.data.userId;
                console.log('获取到的当前userId:', this.currentUserId);
                this.currentnickname = res.data.data.nickname;
                this.currentavatar = res.data.data.avatar;

                // 在获取到用户信息后，初始化WebSocket连接
                this.initWebSocket();
            },
        });
    }
}
</script>

<style>
	.container {
	  display: flex;
	  flex-direction: column;
	  height: 100vh;
	  background:  linear-gradient(45deg, #42c5a4,#8fdbbc,#8adbba);;
	}
	.wrap {
		display: flex;
		/* 使用 flex 布局 */
		align-items: center;
		/* 水平居中 */
		padding: 0 150rpx;
		/* 背景颜色 */
		border: 2px solid #2fa787;
		/* 边框 */
		border-radius: 5px;
		/* 圆角 */
		box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
		/* 阴影效果 */
	}
	
	.chat-box {
	  flex: 1;
	  overflow-y: auto;
	  border: 1px solid #ddd;
	  padding: 10px;
	  background-color: #fff;
	  margin-bottom: 10px;
	}
	
	.message {
	  padding: 5px;
	  border-bottom: 1px solid #eee;
	}
	
	.input-container {
	  display: flex;
	  margin-left: 10px;
	  margin-bottom: 10px;
	}
	
	input {
	  flex: 1;
	  padding: 10px;
	  border: 1px solid #ddd;
	  border-radius: 10px;
	  margin-left: 10px;
	  background: linear-gradient(45deg, #ffffff, #ffffff); /* 设置渐变背景 */
	}
	
	button {
	  padding: 1px 20px; /* 调整按钮的内边距来改变大小 */
	   border: none;
	   border-radius: 20px; /* 增加圆角的半径 */
	   background: linear-gradient(45deg, #7e5b54, #d9ab8c); /* 设置渐变背景 */
	   color: #fff;
	   cursor: pointer;
	   margin-left: 10px;
	   margin-bottom: 10px;
	   margin-right: 10px;
	   font-size: 16px; /* 调整文字大小 */
	   transition: background 0.3s, transform 0.3s; /* 添加过渡效果 */
	}
	
	button:hover {
	  background: linear-gradient(45deg, #3bc2a1, #95dbbf); /* 鼠标悬停时的渐变背景 */
	  transform: scale(1.05); /* 鼠标悬停时略微放大按钮 */
	}
	
	button:active {
	  background: linear-gradient(45deg, #2ebe9d, #96dbc0); /* 按钮被点击时的渐变背景 */
	  transform: scale(0.95); /* 按钮被点击时略微缩小 */
	}
</style>