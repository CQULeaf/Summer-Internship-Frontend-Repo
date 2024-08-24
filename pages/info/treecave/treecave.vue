<template>
	
	<view>
		<!-- 与包裹页面所有内容的元素u-page同级，且在它的下方 -->
		<view>
			<u-navbar class="wrap" height=60 title="树洞" title-size=40 :background="background" is-back=true :customBack="gotopofile">
			</u-navbar>
		</view>
		<view>
			<view>页面建设中1</view>
		</view>
		<u-tabbar v-model="current" :list="list" :mid-button="true"></u-tabbar>
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
								list:'',
								current: 4
			}
		},
		onLoad() {
			this.list = [{
					iconPath: "/static/mailbox.svg",
					text: '信',
					isDot: true,
					customIcon: false,
					pagePath:'/pages/tabbar1'
				},
				{
					iconPath: "/static/refresh.svg",
					text: '溯',
					midButton: true,
					customIcon: false,
					pagePath:'/pages/tabbar3'
				},
				{
					iconPath:  "/static/robot.svg",
					
					selectedIconPath:"/static/mep.png",
					text: '诉',
					isDot: false,
					customIcon: false,
					pagePath:'/pages/me/mypage',
					
				},
			]
		},
		methods: {
	gotopofile(){
		uni.switchTab({
			url:"/pages/info/infopage"
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
</style>