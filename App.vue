<style lang="scss">
 /* 注意要写在第⼀⾏，同时给style标签加⼊lang="scss"属性 */
 @import "uview-ui/index.scss";
 </style>
<script>
export default {
  data() {
    return {
      socketOpen: false,
      socket: null
    };
  },
  methods: {
    connectWebSocket() {
      uni.connectSocket({
        url: 'ws://127.0.0.1:8080/endpoint-websocket',
        header: {
          'content-type': 'application/json',
        },
        success: () => {
//          console.log('WebSocket连接成功');
        },
        fail: (err) => {
          console.error('WebSocket连接失败', err);
        }
      });

      // 监听 WebSocket 打开事件
      uni.onSocketOpen(() => {
        this.socketOpen = true;
        console.log('WebSocket 已连接');
      });

      // 监听 WebSocket 错误事件
      uni.onSocketError((err) => {
        //console.error('WebSocket 错误', err);
        this.socketOpen = false;
        this.reconnectWebSocket();  // 尝试重新连接
      });

      // 监听 WebSocket 关闭事件
      uni.onSocketClose(() => {
        console.log('WebSocket 已关闭');
        this.socketOpen = false;
        this.reconnectWebSocket();  // 尝试重新连接
      });

      // 监听服务器发送来的消息
      uni.onSocketMessage((res) => {
        console.log('收到服务器消息：', res.data);
      });
    },

    // 重新连接WebSocket
    reconnectWebSocket() {
      if (!this.socketOpen) {
        //console.log('尝试重新连接WebSocket');
        this.connectWebSocket();  // 再次尝试连接WebSocket
      }
    },

    // 手动关闭 WebSocket
    closeWebSocket() {
      uni.closeSocket({
        success: () => {
          console.log('WebSocket 已手动关闭');
        },
        fail: (err) => {
          console.error('WebSocket 手动关闭失败', err);
        }
      });
    }
  },
  onLaunch() {
    this.connectWebSocket();  // 应用启动时连接 WebSocket
  },
  onUnload() {
    this.closeWebSocket();  // 应用卸载时关闭 WebSocket
  },
  onHide() {
    this.closeWebSocket();  // 应用进入后台时关闭 WebSocket
  },
  onShow() {
    this.connectWebSocket();  // 应用从后台切换到前台时重新连接 WebSocket
  }
}
</script>

<style lang="scss">
	/*每个页面公共css */
	@import '@/uni_modules/uni-scss/index.scss';
	/* #ifndef APP-NVUE */
	@import '@/static/customicons.css';
	// 设置整个项目的背景色
	page {
		background-color: #f5f5f5;
	}

	/* #endif */
	.example-info {
		font-size: 14px;
		color: #333;
		padding: 10px;
	}
</style>
