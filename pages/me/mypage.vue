<template>
	<view >
		<view v-if="logined" v-model="this.user" >
			<view class="u-demo-wrap">
				<view class="u-demo-area">
					<u-toast ref="uToast"></u-toast>
					<view class="u-avatar-wrap">
						<image @click="chooseAvatar" class="u-avatar-demo" v-if="this.user.avatar"
							:src="this.user.avatar" mode="aspectFill"></image>
						<view>{{this.user.nickname}}</view>
					</view>
				</view>
			</view>

			<u-grid :col="8" :border="false">
				<u-grid-item @click="gotoconcern('friends')">
					<view class="grid-text">朋友</view>
				</u-grid-item>
				<u-grid-item @click="gotoconcern('follows')">
					<view class="grid-text">关注</view>
				</u-grid-item>
				<u-grid-item @click="gotoconcern('fans')">
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

			<view class="u-p-t-20">
				<view class="all-reply-top">全部帖子</view>
				<view class="list-item" v-for="(item, index) in postList" :key="index" @click="goToPost(item)">
					<view>
						<text class="item-title">{{item.title}}</text>
					</view>
				</view>
			</view>
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
					username: '',
					userId: ''
				},
				pic: 'https://uviewui.com/common/logo.png',
				show: true,
				// 定义一个变量
				logined: true,
				list: '',
				current: 4,
			
				swiperCurrent: 0,
				currentItems: [], // 当前用户列表数据
				dataCache: {},
				loading: false,
				page: 1, // 当前页码
				hasMore: true, // 是否还有更多数据
				touchStartX: 0,
				touchEndX: 0,
				touchThreshold: 30 ,//处理滑动
				
				postList:[{
					title:'',
					
				}]
			};
		},
		onLoad() {
			const value = uni.getStorageSync('nowAccount');
			this.user = value.data
			this.logined = (value.code == 200)
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
					pagePath: '/pages/corner/corner'
				},
				{
					iconPath: "/static/yanblack.png",
					selectedIconPath: "/static/yanpink.png",
					text: '言',
					midButton: true,
					customIcon: false,
					pagePath: '/pages/post/postpage'
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
			uni.getStorage({
				key: 'nowAccount',
				success: (ret) => {
					//console.log(ret.data.data.userId);
					this.user = ret.data.data

					//判断用户是否登录
					this.logined = (ret.data.code == 200)
					console.log(this.user);
					// //获取用户帖子信息
					uni.request({
						url:"http://localhost:8080/ccPost/mypost",
						data: {
							user_id: ret.data.data.userId
						},
						success: (res) => {
							console.log(res.data.data);
							this.postList = res.data.data
							console.log(this.postList);
						},
						fail(res2) {
							console.log(ret.data.data.userId)
							console.log(res2);
						}
					})
				}
			})

			const value = uni.getStorageSync('nowAccount');
			this.user = value.data
			this.logined = (value.code == 200)
		},
		created() {
			// 监听从裁剪页发布的事件，获得裁剪结果
			uni.$on('uAvatarCropper', path => {
				const value = uni.getStorageSync('nowAccount');
				value.data.avatar = path
				this.user.avatar = path
				console.log(path)
				uni.setStorageSync('nowAccount', value)
				// 可以在此上传到服务端

				const username = this.user.username;
				const filePath = path;

				uni.uploadFile({
					url: 'http://localhost:8080/user/updateAvatar',
					filePath: filePath,
					name: 'file', // 对应后端接收文件的字段名
					formData: {
						'username': username
					},

					success: (res) => {
						console.log(res.data);
					},
					fail: (err) => {
						console.error(err);
					}
				});

			});
		},

		methods: {
			goToPost(item) {
				uni.setStorage({
					key: "postData",
					data: item,
					success() {
						uni.navigateTo({
							url: "/pages/home/reply"
						})
					}
				})
			},
			goToLiked(item) {
				// 跳转到聊天界面，假设聊天界面的路径为 '/pages/info/treecave/treetalk'
				uni.navigateTo({
					url: '/pages/me/myliked?post_id=' + item.post_id // 假设用户有一个 id 属性
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
			gotoconcern(type) {
				uni.navigateTo({
					url: `/pages/me/concern?type=${type}` // 传递用户类别  
				});
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
	};
</script>

<style lang="scss">
	.comment {
		padding: 30rpx;
		font-size: 32rpx;
		background-color: #ffffff;
	}
	.container {
		background-color: #ffc9d3;
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
		color: u-type-info;
	}

	page {
		background-color: #ffffff;
	}

	.camera {
		width: 54px;
		height: 44px;

    :active {
			background-color: #ededed;
		}
	}

	.user-box {
		background-color: #fff;
	}

	.u-demo-wrap {
		background-color: #ffd2d9;
		padding: 5px;
		
	}

	.u-avatar-demo {
		width: 150rpx;
		height: 150rpx;
		border-radius: 100rpx;
	}

	.u-avatar-wrap {
		padding: 5px;
		overflow: hidden;
		margin-bottom: 80rpx;
		text-align: center;
		margin-top: 40px;
	}

	.list-item {
		display: flex;
		align-items: center;
		padding: 25px;
		border-bottom: 1px solid #f0dddf;
		background-color: #ffeff2;
	}

	.item-title {
		font-size: 13px;
		color: #333;
	}

	.all-reply-top {
		margin-left: 20rpx;
		padding-left: 20rpx;
		border-left: solid 4rpx #f0dddf;
		font-size: 30rpx;
		font-weight: bold;
	}
</style>