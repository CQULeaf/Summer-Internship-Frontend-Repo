<template>
	<view>
	<view v-if="logined">
		<view>
			<view class="u-m-r-10 u-avatar-wrap">
				<image @tap="preAvatar" class="u-avatar-demo" :src="this.user.avatar"></image>
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
	  <u-tabbar v-model="current" :list="list" :mid-button="true"></u-tabbar>
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
				pic: 'https://uviewui.com/common/logo.png',
				show: true,
				// 定义一个变量
				logined:true,
				list: '',
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
		methods: {
			fetchUser() {
				uni.request({
					url: "http://127.0.0.1:4523/m1/5010181-4669608-default/user/me", //在fetchUserList方法中的请求URL使用了'${apiEndpoint}?page=${page}'，这会导致字符串直接拼接而非动态生成变量。正确的做法应该是使用模板字符串的语法来动态替换变量
					data:this.user,
					method: 'GET',
					success: (res) => {
						if (res.statusCode === 200) {
						  console.log(res)
							}
						else {
							uni.showToast({
								title: '获取数据失败',
								icon: 'none'
							});
						}
					},
					fail: (err) => {
						uni.showToast({
							title: '请求失败',
							icon: 'none'
						});
					},
					complete: () => {
						this.loading = false;
					}
				});
			},
			goToProfile() {
				uni.navigateTo({
					url: '/pages/me/setting'
				});
			},
			login() {
				console.log("登录")
				//跳转到登录页面
				uni.navigateTo({
					url: "/pages/me/login"
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
			},
			preAvatar() {
				wx.previewImage({
					current: '', // 当前显示图片的 http 链接
					urls: [this.user.avatar] // 需要预览的图片 http 链接列表
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
