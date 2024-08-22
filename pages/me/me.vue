<template>
	<view>
	<view v-if="logined">
		<view>
			<view class="u-m-r-10 u-avatar-wrap">
				<image class="u-avatar-demo" :src="this.user.avatar"></image>
			</view>
		</view>
		
		<u-grid :col="8" :border="false">
			<u-grid-item @click="gotoconcern">
				<view class="grid-text">朋友</view>
			</u-grid-item>
			<u-grid-item @click="gotoconcern">
				<view class="grid-text">关注</view>
			</u-grid-item>
			<u-grid-item @click="gotoconcern">
				<view class="grid-text">粉丝</view>
			</u-grid-item>
			<u-grid-item></u-grid-item>
			<u-grid-item></u-grid-item>
			<u-grid-item></u-grid-item>
			<u-grid-item></u-grid-item>
			<u-grid-item>
				<u-icon name="setting" :size="50" @click="gotoSetting"></u-icon>
			</u-grid-item>
		</u-grid>
	</view>
	<view v-else>
		<u-navbar :is-back="false" title="　" :border-bottom="false">
			<view class="u-flex u-row-right" style="width: 100%;">
				<view class="camera u-flex u-row-center">
					<u-icon name="camera-fill" color="#000000" size="48"></u-icon>
				</view>
			</view>
		</u-navbar>
		<view class="u-flex user-box u-p-l-30 u-p-r-20 u-p-b-30">
			<view class="u-m-r-10">
				<u-avatar :src="pic" size="140"></u-avatar>
			</view>
			<view class="u-flex-1">
				<view class="u-font-18 u-p-b-20">未登录</view>
			</view>
		</view>
		
		
		<view class="u-m-t-20">
			<u-cell-group>
				<!-- 点击事件 -->
				<u-cell-item icon="man-add" title="登录" @click="login()"></u-cell-item>
			</u-cell-group>
		</view>
	  </view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				href: 'https://uniapp.dcloud.io/component/README?id=uniui',
				user: {
					avatar: '/static/logo.png',
					nickname: '1111'
				},
				pic:'https://uviewui.com/common/logo.png',
				show:true,
				// 定义一个变量
				logined:true
			}
		},
		onLoad() {
			
		},
		methods: {
			login(){
				console.log("登录")
				//跳转到登录页面
				uni.navigateTo({
					url:"/pages/me/login"
				})
			},
			gotoSetting(){
				uni.navigateTo({
					url:"/pages/me/setting/setting"
				})
			},
			gotoconcern(){
				uni.navigateTo({
					url:"/pages/me/concern"
				})
			}
		}
	}
</script>

<style lang="scss">
	.myinfo {
		display: flex;
		align-items: center;
		padding: 50rpx;
		background-image: linear-gradient(to top left, #ffcbcc, #ffe4e6);
	}

	.custom-style {
			color: #606266;
			width: 400rpx;
	}
		
	.myavatar {
		width: 150rpx;
		height: 150rpx;
		border-radius: 50%;
		margin-right: 50rpx;
	}
	
	.grid-text {
			font-size: 32rpx;
			margin-top: 4rpx;
			color: $u-type-info;
	}

	.mynickname {
		font-size: 28rpx;
		color: #333;
	}
	page{
		background-color: #ffffff;
	}

.camera{
	width: 54px;
	height: 44px;
	
	&:active{
		background-color: #ededed;
	}
}
.user-box{
	background-color: #fff;
}

.u-avatar-demo {
	width: 150rpx;
	height: 150rpx;
	border-radius: 100rpx;
}

.u-avatar-wrap {
	margin-top: 80rpx;
	overflow: hidden;
	margin-bottom: 80rpx;
	text-align: center;
}
</style>
