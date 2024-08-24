<template>
	<view>
		<view v-if="logined" v-model="this.user">
			<view class="u-demo-wrap">
				<view class="u-demo-area">
					<u-toast ref="uToast"></u-toast>
					<view class="u-avatar-wrap">
						<image @click="chooseAvatar" class="u-avatar-demo" v-if="this.user.avatar" :src="this.user.avatar" mode="aspectFill"></image>
						<view>{{this.user.nickname}}</view>
					</view>
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
					avatar: '',
					nickname: '1111',
					username:''
				},
				pic: 'https://uviewui.com/common/logo.png',
				show: true,
				// 定义一个变量
				logined: true,
				list: '',
				current: 4
			}
		},
		onLoad() {
			const value = uni.getStorageSync('nowAccount');
			this.user=value.data
			this.logined=(value.code==200)
			console.log(value.code)
			this.list = [{
					iconPath: "/static/newhomeg.png",
					selectedIconPath: "/static/newhomep.png",
					text: '家',
					isDot: true,
					customIcon: false,
					pagePath: '/pages/home/homepage'
				},
				{
					iconPath: "/static/happygrey.png",
					selectedIconPath: "/static/happierp.png",
					text: '聚',
					isDot: true,
					customIcon: false,
					pagePath: '/pages/tabbar'
				},
				{
					iconPath: "/static/yanblack.png",
					selectedIconPath: "/static/yanpink.png",
					text: '言',
					midButton: true,
					customIcon: false,
					pagePath: '/pages/tabbar'
				},
				{
					iconPath: "/static/messagegrey.png",
					selectedIconPath: "/static/messagep.png",
					text: '讯',
					customIcon: false,
					pagePath: '/pages/info/infopage'
				},
				{
					iconPath: "/static/megrey.png",
					selectedIconPath: "/static/mep.png",
					text: '我',
					isDot: false,
					customIcon: false,
					pagePath: '/pages/me/mypage'
				},
			]
		},
		onShow() {
			//判断用户是否登录
			const value = uni.getStorageSync('nowAccount');
			this.user=value.data
			this.logined=(value.code==200)
			// const avatarData = uni.getStorageSync('avatarData')
			// this.user.avatar=avatarData
			// uni.removeStorageSync('avatarData')
			//console.log(this.user.avatar)
		},
		created() {
			// 监听从裁剪页发布的事件，获得裁剪结果
			uni.$on('uAvatarCropper', path => {
				const value = uni.getStorageSync('nowAccount');
				value.data.avatar=path
				uni.setStorageSync('nowAccount',value)
				// 可以在此上传到服务端
				uni.uploadFile({
					url: 'http://localhost:1234/user/updateAvatar',
					filePath: path,
					formData: this.user.username,
					name: 'file',
					complete: (res) => {
						console.log(res);
					}
				});
			})
			//console.log(this.user.avatar)
			uni.request({
				url:'http://localhost:1234/user/updateAvatar',
				data:this.user,
				method:'POST',
				header:{
					'Content-Type': 'application/json'
				},
			})
		},
		methods: {
			goToProfile() {
				uni.navigateTo({
					url: '/pages/me/setting'
				});
			},
			login() {
				console.log("登录")
				//跳转到登录页面
				uni.navigateTo({
					url: "/pages/login"
				})
			},
			gotoSetting() {
				uni.navigateTo({
					url: "/pages/me/setting/setting"
				})
			},
			gotoconcern() {
				uni.navigateTo({
					url: "/pages/me/concern"
				})
			},
			quit() {
				console.log("退出"),
				this.logined = false
			},
			chooseAvatar() {
				this.$u.route({
					url: '/uview-ui/components/u-avatar-cropper/u-avatar-cropper',
					params: {
						// 输出图片宽度，高等于宽，单位px
						destWidth: 300,
						// 裁剪框宽度，高等于宽，单位px
						rectWidth: 200,
						// 输出的图片类型，如果'png'类型发现裁剪的图片太大，改成"jpg"即可
						fileType: 'jpg',
					}
				})
			},
		},
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

	page {
		background-color: #ffffff;
	}

	.camera {
		width: 54px;
		height: 44px;

		&:active {
			background-color: #ededed;
		}
	}

	.user-box {
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