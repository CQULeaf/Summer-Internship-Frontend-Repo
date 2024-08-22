<template>
	<view class="container">
		<!-- 用户信息部分 -->
		<view class="myinfo">
			<image class="myavatar" :src="user.avatar"></image>
			<text class="mynickname">{{ user.nickname }}</text>
			<button size="default" style="background-color:#ffb9c0;color: #000000" @click="goToProfile">去设置</button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				href: 'https://uniapp.dcloud.io/component/README?id=uniui',
				user: {
					avatar: '/static/c1.png',
					nickname: '1111'
				}
			}
		},
		methods: {
	
		}
	}
	
</script>

<style>
	.myinfo {
		display: flex;
		align-items: center;
		padding: 50rpx;
		background-image: linear-gradient(to top left, #ffcbcc, #ffe4e6);
	}

	.myavatar {
		width: 150rpx;
		height: 150rpx;
		border-radius: 50%;
		margin-right: 50rpx;
	}

	.mynickname {
		font-size: 28rpx;
		color: #333;
	}
</style>