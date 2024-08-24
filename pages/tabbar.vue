<template>
	<view>
		<!-- 与包裹页面所有内容的元素u-page同级，且在它的下方 -->
		<view>
			<!-- <view v-if="list"></view> -->
		</view>
		<u-tabbar v-model="current" :list="list" :mid-button="true"></u-tabbar>
	</view>
</template>
<script>
	export default {
		data() {
			return {
				list: [{
						iconPath: "/static/newhomeg.png",
						selectedIconPath: "/static/newhomep.png",
						text: '家',
						customIcon: false,
					},
					{
						iconPath:  "/static/happygrey.png",
						selectedIconPath:"/static/happierp.png",
						text: '聚',
						customIcon: false,
					},
					{
						iconPath: "/static/yanblack.png",
						selectedIconPath: "/static/yanpink.png",
						text: '言',
						midButton: true,
						customIcon: false,
					},
					{
						iconPath:  "/static/messagegrey.png",
						selectedIconPath:"/static/messagep.png",
						text: '讯',
						customIcon: false,
					},
					{
						iconPath:  "/static/megrey.png",
						selectedIconPath:"/static/mep.png",
						text: '我',
						isDot: false,
						customIcon: false,
					},
				],
				current: 0
			}
		},
		
		onLoad() {
			this.list = [{
					iconPath: "/static/newhomeg.png",
					selectedIconPath: "/static/newhomep.png",
					text: '家',
					isDot: true,
					customIcon: false,
					pagePath:'/pages/tabbar'
				},
				{
					iconPath:  "/static/happygrey.png",
					selectedIconPath:"/static/happierp.png",
					text: '聚',
					isDot: true,
					customIcon: false,
					pagePath:'/pages/tabbar'
				},
				{
					iconPath: "/static/yanblack.png",
					selectedIconPath: "/static/yanpink.png",
					text: '言',
					midButton: true,
					customIcon: false,
					pagePath:'/pages/tabbar'
				},
				{
					iconPath:  "/static/messagegrey.png",
					selectedIconPath:"/static/messagep.png",
					text: '讯',
					customIcon: false,
					pagePath:'/pages/tabbar'
				},
				{
					iconPath:  "/static/megrey.png",
					selectedIconPath:"/static/mep.png",
					text: '我',
					isDot: false,
					customIcon: false,
					pagePath:'/pages/me/me'
				},
			]
		},
	}
</script>
