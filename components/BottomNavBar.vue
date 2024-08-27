<template>
	<view class="bottom-nav-bar">
		<view v-for="(item, index) in navItems" :key="index" @click="navigateTo(item.pagePath)" class="nav-item">
			<image :src="item.icon" class="nav-icon" />
			<text class="nav-text">{{ item.text }}</text>
		</view>
	</view>
</template>

<script>
	export default {
		props: {
			navItems: {
				type: Array,
				default: () => []
			}
		},
		methods: {
			navigateTo(pagePath) {
				uni.navigateTo({
					url: pagePath
				});
			}
		}
	};
</script>

<style>
	.bottom-nav-bar {
		display: flex;
		justify-content: space-around;
		height: 60px;
		/* 设置导航栏高度 */
		background-color: #ffffff;
		/* 背景颜色 */
		border-top: 1px solid #e0e0e0;
		/* 边框 */
	}

	.nav-item {
		display: flex;
		flex-direction: column;
		/* 垂直排列图标和文本 */
		flex: 1;
		text-align: center;
		align-items: center;
	}

	.nav-icon {
		width: 40px;
		/* 图标宽度 */
		height: 40px;
		/* 图标高度 */
	}

	.nav-text {
		font-size: 12px;
		/* 文本大小 */
		color: #333;
		/* 文本颜色 */
	}
</style>