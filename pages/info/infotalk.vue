<template>  
	<view class="container">  
		<u-navbar   
			class="wrap"   
			height=60   
			title="聊天"   
			title-size=40   
			:background="background"   
			is-back=true  
			:customBack="gotopofile">  
		</u-navbar>  
		<view class="chat-box" ref="chatBox">  
			<view class="message" v-for="(msg, index) in messages" :key="index">  
				<strong>{{ msg.senderId }}:</strong> {{ msg.content }}  
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
				currentUserId: '',  
				receiverId: ''  
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
					this.messages.push(message); // 推送解析后的消息对象  
					this.$nextTick(() => {  
						const chatBox = this.$refs.chatBox;  
						chatBox.scrollTop = chatBox.scrollHeight; // 滚动到底部  
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
						senderId: this.currentUserId, // 发件人 ID  
						receiverId: this.receiverId,   // 收件人 ID  
						content: this.messageInput,     // 消息内容  
						createdAt: new Date()           // 创建时间  
					};  
					this.ws.send(JSON.stringify(message)); // 发送消息  
					this.messageInput = ''; // 清空输入框  
				}  
			},  
			gotopofile() {  
				uni.switchTab({  
					url: "/pages/info/infopage"  
				})  
			}  
		},  
		mounted() {  
			this.receiverId = this.$route.query.userId; // 获取 UID  
			console.log('获取到的 userId:', this.receiverId);  
			uni.getStorage({  
				key: 'nowAccount',  
				success: (res) => {  
					this.currentUserId = res.data.data.userId; // 获取当前用户 ID  
					console.log('获取到的当前userId:', this.currentUserId);  
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
		background: linear-gradient(45deg, #42c5a4, #8fdbbc, #8adbba);
	}

	.wrap {
		display: flex;
		align-items: center;
		padding: 0 150rpx;
		border: 2px solid #2fa787;
		border-radius: 5px;
		box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
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
		background: linear-gradient(45deg, #ffffff, #ffffff);
	}

	button {
		padding: 1px 20px;
		border: none;
		border-radius: 20px;
		background: linear-gradient(45deg, #7e5b54, #d9ab8c);
		color: #fff;
		cursor: pointer;
		margin-left: 10px;
		font-size: 16px;
		transition: background 0.3s, transform 0.3s;
	}

	button:hover {
		background: linear-gradient(45deg, #3bc2a1, #95dbbf);
		transform: scale(1.05);
	}

	button:active {
		background: linear-gradient(45deg, #2ebe9d, #96dbc0);
		transform: scale(0.95);
	}
</style>