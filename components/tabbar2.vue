<template>
	<view>
		<!-- 与包裹页面所有内容的元素u-page同级，且在它的下方 -->
		<view>
			<view>页面建设中2</view>
		</view>
		<u-tabbar v-model="current" :list="list" :mid-button="true"></u-tabbar>
	</view>
</template>
<script>
	export default {
		data() {
			return {
				list:'',
				current: 4
			}
		},
		
		onLoad() {
			this.list = [{
					iconPath: "/static/newhomeg.png",
					selectedIconPath: "/static/newhomep.png",
					text: '家',
					isDot: true,
					customIcon: false,
					pagePath:'/pages/tabbar1'
				},
				{
					iconPath:  "/static/happygrey.png",
					selectedIconPath:"/static/happierp.png",
					text: '聚',
					isDot: true,
					customIcon: false,
					pagePath:'/pages/tabbar2'
				},
				{
					iconPath: "/static/yanblack.png",
					selectedIconPath: "/static/yanpink.png",
					text: '言',
					midButton: true,
					customIcon: false,
					pagePath:'/pages/tabbar3'
				},
				{
					iconPath:  "/static/messagegrey.png",
					selectedIconPath:"/static/messagep.png",
					text: '讯',
					customIcon: false,
					pagePath:'/pages/info/infopage'
				},
				{
					iconPath:  "/static/megrey.png",
					selectedIconPath:"/static/mep.png",
					text: '我',
					isDot: false,
					customIcon: false,
					pagePath:'/pages/me/mypage'
				},
			]
		},
	}
</script>
