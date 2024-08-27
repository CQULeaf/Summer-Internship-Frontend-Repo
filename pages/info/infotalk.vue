<template>
	<view class="container">
		<u-navbar class="wrap" height=60 title="" title-size=40 :background="background" is-back=true
			:customBack="gotopofile">
		</u-navbar>

		<view class="chat-box" ref="chatBox">
			<view v-for="(msg, index) in messages" :key="index">
				<view :class="{'message-me': msg.senderId === currentUserId, 'message-other': msg.senderId !== currentUserId}">
					<strong>{{ msg.senderId === currentUserId ? currentnickname : receivernickname }}</strong>
				</view>
				<image :class="{'message-myavatar': msg.senderId === currentUserId, 'message-avatar': msg.senderId !== currentUserId}" :src="msg.senderId === currentUserId ? currentavatar : receiveravatar">
				</image>
				<view class="message"
					:class="{'my-message': msg.senderId === currentUserId, 'other-message': msg.senderId !== currentUserId}">
					{{ msg.content }}
				</view>
			</view>
		</view>
		<view class="input-container">
			<input v-model="messageInput" placeholder="编辑你的内容..." />
			<button @click="sendMessage">发送</button>
		</view>
	</view>
</template>

<script>
	import {
		Stomp
	} from '../../js/stomp.js'; // 引入 STOMP 客户端库
	export default {
		data() {
			return {
				background: {
					backgroundColor: '#001f3f',
					backgroundSize: 'cover',
					backgroundImage: 'linear-gradient(45deg, rgb(159, 219, 196),rgb(139, 219, 186),rgb(35, 187, 154))'
				},
				stompClient: null, // STOMP 客户端实例
				messages: [], // 存储消息
				messageInput: '', // 输入框绑定值
				currentUserId: '', // 当前用户ID
				receiverId: '', // 接收者ID
				currentnickname: '',
				receivernickname: '',
				currentavatar: '',
				receiveravatar:'',
				otheruser: {}
			}
		},
		onReady() {
			this.initWebSocket();
		},
		methods: {
			initWebSocket() {
				// 创建 WebSocket 连接  
				const socket = new WebSocket('ws://127.0.0.1:1234/endpoint-websocket');
				this.stompClient = Stomp.over(socket);

				this.stompClient.connect({}, frame => {
					console.log('STOMP连接成功:', frame);

					// 订阅私人消息队列
					this.stompClient.subscribe('/user/' + this.currentUserId + '/private', message => {
						console.log(message.body);
						this.messages.push(JSON.parse(message.body)); // 将消息添加到消息列表
						// 自动滚动到底部
						this.$nextTick(() => {
							const chatBox = this.$refs.chatBox;
							chatBox.scrollTop = chatBox.scrollHeight;
						});
					});
				}, error => {
					console.error('STOMP连接错误:', error);
					this.reconnectWebSocket(); // 尝试重新连接
				});
			},

			sendMessage() {
				if (this.messageInput.trim() !== '') {
					let message = {
						senderId: this.currentUserId, // 发件人ID
						receiverId: this.receiverId, // 收件人ID
						content: this.messageInput, // 消息内容
						createdAt: new Date() // 创建时间
					};
					console.log('发送的消息:', message); // 打印消息内容
					this.messages.push(message);
					this.stompClient.send('/app/private', {}, JSON.stringify(message)); // 确保消息被序列化为 JSON 字符串
					this.messageInput = '';
				}
			},

			reconnectWebSocket() {
				if (!this.stompClient.connected) {
					console.log('尝试重新连接WebSocket');
					this.initWebSocket(); // 再次尝试连接WebSocket
				}
			},

			gotopofile() {
				uni.switchTab({
					url: "/pages/info/infopage"
				})
			},
			getMessage() {
				uni.request({
					url: `http://localhost:1234/message/historyWithUser?userId=${this.currentUserId}&targetUserId=${this.receiverId}`,
					method: 'GET',
					success: (res) => {
						if (res.statusCode === 200) {
							this.messages = res.data.data
							console.log("获取的消息", this.messages)
						} else {
							reject(
								`获取消息记录失败: ${res.statusCode}`
							);
						}
					},
					fail: (err) => {
						reject('请求失败');
					}
				});
			},

			getOthernUser() {
				uni.request({
					url: `http://localhost:1234/user/getUserInfo?userId=${ this.receiverId}`,
					method: 'GET',
					success: (res) => {
						if (res.statusCode === 200) {
							this.otheruser = res.data.data
							console.log("获取其他用户", this.otheruser)
							this.receivernickname=this.otheruser.nickname
							this.receiveravatar=this.otheruser.avatar
						} else {
							reject(
								`获取消息记录失败: ${res.statusCode}`
							);
						}
					},
					fail: (err) => {
						reject('请求失败');
					}
				});
			}
		},

		mounted() {
			this.receiverId = this.$route.query.userId; // 获取目标用户ID（私聊对象）
			console.log('获取到的 userId:', this.receiverId);
			this.getOthernUser()
			uni.getStorage({
				key: 'nowAccount',
				success: (res) => {
					this.currentUserId = res.data.data.userId; // 获取当前用户ID
					console.log('获取到的当前userId:', this.currentUserId);
					this.currentnickname = res.data.data.nickname;
					this.currentavatar = res.data.data.avatar;
					console.log('获取到的当前头像:', this.currentavatar);
				},
			});
			this.getMessage();
		}
	}
</script>

<style>
	.container {
		display: flex;
		flex-direction: column;
		height: 100vh;
		background: linear-gradient(45deg, #6ed2b1, #73d4b3, #42c5a4);
	}

	.wrap {
		display: flex;
		align-items: center;
		padding: 0 150rpx;
		border: 2px solid #2fa787;
		border-radius: 5px;
		box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
	}

	.message-me {
		display: flex;
		margin-left: 610rpx;
		/* 居中对齐 */
		font-size: 16px;
		/* 字体大小 */
		color: #333;
		/* 字体颜色 */
		background-color: #ffffff;
	}
	.message-other {
		display: flex;
		margin-right: 590rpx;
		/* 居中对齐 */
		font-size: 16px;
		/* 字体大小 */
		color: #333;
		/* 字体颜色 */
		background-color: #ffffff;
	}

	.message-myavatar {
		width: 100rpx;
		height: 100rpx;
		border-radius: 50%;
		margin-top: 3px;
		margin-right: 20px;
		margin-left: 610rpx;
	}
	.message-avatar {
		width: 100rpx;
		height: 100rpx;
		border-radius: 50%;
		margin-top: 3px;
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
		display: inline-block;
		/* 使消息框根据内容自动扩展 */
		padding: 10px;
		/* 内边距 */
		border-radius: 10px;
		/* 边角圆润 */
		/* 最大宽度，防止过宽 */
		word-wrap: break-word;
		/* 允许单词换行 */
		white-space: pre-wrap;
		/* 保留空格和换行 */
		margin-bottom: 20px;
		/* 消息之间的间距 */

	}

	.my-message {
		max-width: 60%;
		margin-left: auto;
		/* 右对齐 */
		background: linear-gradient(45deg, #cdeedf, #8adbba, #8fdbbc);
		/* 自己的消息背景色 */
		display: block;
		/* 改为 block 使其占满一行 */


	}

	.other-message {
		max-width: 60%;
		margin-right: auto;
		/* 左对齐 */
		background-color: ;
		background: linear-gradient(45deg, #f8c2c7, #f8ced1, #f8d7da);
		/* 对方的消息背景色 */
		display: block;
		/* 改为 block 使其占满一行 */
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
		background: linear-gradient(45deg, #8f675f, #b8857b, #d9ab8c);
		color: #fff;
		cursor: pointer;
		margin-left: 10px;
		font-size: 16px;
		margin-right: 10px;
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