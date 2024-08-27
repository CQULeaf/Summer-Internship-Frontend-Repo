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
	export default {
		data() {
			return {

				background: {
					backgroundColor: '#001f3f',
					backgroundSize: 'cover',
					backgroundImage: 'linear-gradient(45deg, rgb(159, 219, 196),rgb(139, 219, 186),rgb(35, 187, 154))'
				},
				ws: null, // WebSocket 实例
				messages: [], // 存储消息
				messageInput: '', // 输入框绑定值
			}
		},
		onReady() {
			this.initWebSocket();
		},
		methods: {
			initWebSocket() {
				// 创建 WebSocket 连接
				this.ws = new WebSocket('ws://127.0.0.1:8080/endpoint-websocket');

				// 处理 WebSocket 消息
				this.ws.onmessage = (event) => {
					let message = JSON.parse(event.data);
					this.messages.push(event.data);
					this.$nextTick(() => {
						// 滚动到聊天框底部
						const chatBox = this.$refs.chatBox;
						chatBox.scrollTop = chatBox.scrollHeight;
					});
				};

				// 处理 WebSocket 错误
				this.ws.onerror = (error) => {
					console.error('WebSocket Error: ', error);
				};

				// 处理 WebSocket 关闭
				this.ws.onclose = () => {
					console.log('WebSocket closed');
				};
			},
			sendMessage() {
				if (this.messageInput.trim() !== '') {
					let message = {
						senderId: this.currentUserId,
						receiverId: this.receiverId,
						content: this.messageInput,
						createdAt: new Date()
					};
					this.ws.send(JSON.stringify(message));
					this.messageInput = ''; // 清空输入框
				}
			},
			gotopofile() {
				uni.navigateTo({
					url: "/pages/info/treecave/treecave"
				})
			}
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