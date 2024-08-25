<template>

	<view>
		<view>
			<u-navbar class="wrap" height=60 title="树洞" title-size=40 :background="background" is-back=true
				:customBack="gotopofile">
			</u-navbar>
		</view>
		<view class="image-container">
			<image class="image" src="/static/forest.jpg"></image>
			<button class="center-button">开始相遇</button>
		</view>
		<bottom-nav-bar :navItems="navItems" />
	</view>
</template>

<script>
	import BottomNavBar from '@/components/BottomNavBar.vue'; // 引入组件
	export default {
		components: {
			BottomNavBar // 注册组件
		},
		data() {
			return {

				background: {
					backgroundColor: '#001f3f',
					backgroundSize: 'cover',
					backgroundImage: 'linear-gradient(45deg, rgb(159, 219, 196),rgb(139, 219, 186),rgb(35, 187, 154))'
				},
				navItems: [{
						pagePath: '/pages/info/treecave/treepost',
						text: '信',
						icon: '/static/mailbox.svg'
					},
					{
						pagePath: '/pages/info/treecave/treecave',
						text: '溯',
						icon: '/static/refresh.svg'
					},
					{
						pagePath: '/pages/info/treecave/ai',
						text: '诉',
						icon: '/static/robot.svg'
					}
				]
			}
		},

		methods: {
			gotopofile() {
				uni.switchTab({
					url: "/pages/info/infopage"
				})
			}
		}
	}
</script>

<style>
	.wrap {
		display: flex;
		/* 使用 flex 布局 */
		align-items: center;
		/* 水平居中 */
		padding: 0 110rpx;
		/* 背景颜色 */
		border: 2px solid #2fa787;
		/* 边框 */
		border-radius: 5px;
		/* 圆角 */
		box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
		/* 阴影效果 */
	}

	.container {
		display: flex;
		flex-direction: column;
		height: 100vh;
		/* 使容器占满整个视口 */
	}

	.image-container {
		position: relative;
		/* 相对定位使按钮能够绝对定位在其中 */
		flex: 1;
		/* 使图片区域占满剩余空间 */
	}

	.navbar {
		flex-shrink: 0;
		/* 防止导航栏收缩 */
	}

	.content {
		flex: 1;
		/* 内容区域占满剩余空间 */
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.image {
		width: 200vw;
		/* 视口宽度 */
		height: 80vh;
		/* 视口高度 */
		object-fit: cover;
		/* 保持比例且覆盖整个区域 */
		background-position: center;
		/* 居中显示 */
	}
	.center-button {
	    position: absolute; /* 绝对定位按钮 */
	    top: 40%; /* 垂直居中 */
	    left: 50%; /* 水平居中 */
	    transform: translate(-50%, -50%); /* 将按钮中心对齐到图片中心 */
	    padding: 10px 20px; /* 按钮内边距 */
	    background-color: rgba(51, 153, 119, 1.0); /* 背景颜色（可选） */
	    color: #ffffff; /* 文字颜色 */
	    border: none; /* 边框（可选） */
	    border-radius: 50px; /* 圆角（可选） */
	    cursor: pointer; /* 鼠标悬停时的光标样式 */
	  }

	.bottom-nav-bar {
		margin-top: auto;
		/* 将底部导航栏推到容器底部 */
	}
</style>