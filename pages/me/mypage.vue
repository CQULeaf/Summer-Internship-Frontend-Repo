<template>
	<view class="container">
		<view v-if="logined" v-model="this.user">
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
			<view class="slot-wrap"><!-- 通过自定义slot传入的内容 -->
				<u-tabs :list="sublist" :is-scroll="false" :current="subcurrent" @change="change" active-color="#000000"
					inactive-color="#606266" item-width=140 bg-color="#ffc7cb" :bold="false"></u-tabs>
			</view>
			<scroll-view class="content" scroll-y>
				<view v-if="subcurrent === 0" class="list">
					<view v-if="posts.length === 0" class="loading">加载中...</view>
					<view v-for="(post, index) in posts" :key="index" class="list-item">
						<image class="useravatar" :src="post.avatar" />
						<text class="nickname">{{ post.username }}</text>
					</view>
				</view>
				<view v-else-if="subcurrent===1" class="list">
					<view v-if="liked.length === 0" class="loading">加载中...</view>
					<view v-for="(like, index) in liked" :key="index" class="list-item">
						<image class="useravatar" :src="like.avatar" />
						<text class="nickname">{{ like.username }}</text>
					</view>
				</view>
			</scroll-view>
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
				sublist: [
					// 标签数据
					{
						name: '帖子',

					},
					{
						name: '点赞',

					}
				],
				subcurrent: 0,
				posts: [],
				liked: [],
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
			//判断用户是否登录
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
					url: 'http://47.120.1.65:1234/user/updateAvatar',
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
			change(index) {
				this.subcurrent = index;
				if (index === 0) { // 如果点击的是“朋友”
					this.getposts(); // 获取朋友列表
				} else if (index === 1) {
					this.getliked();
				}
			},
			getposts() {
				uni.request({
					url: 'http://localhost:8080/ccPost/mypost',
					data: this.user,
					method: 'GET',
					success: (res) => {
						console.log(res)
						if (res.statusCode === 200) {
							this.posts = res.data.data
						} else {
							console.error('获取帖子列表失败:', res);
						}
					},
					fail: (err) => {
						console.error('请求失败:', err);
					}
				})
			},
			getliked() {
				uni.request({
					url: 'http://localhost:8080/user/getFollowedPosts',
					data: this.user,
					method: 'GET',
					success: (res) => {
						console.log(res)
						if (res.statusCode === 200) {
							this.liked = res.data.data
							this.fetchUserLiked(this.liked);
						} else {
							console.error('获取点赞列表失败:', res);
						}
					},
					fail: (err) => {
						console.error('请求失败:', err);
					}
				})
			},
			fetchUserLiked(users) {
				const promises = users.map(user => {
					return new Promise((resolve, reject) => {
						uni.request({
							url: `http://localhost:8080/user/getUserInfo?userId=${userId}`, // 假设你有一个获取用户信息的 API
							method: 'GET',
							success: (res) => {
								if (res.statusCode === 200) {
									resolve(res.data); // 返回用户信息
								} else {
									reject('获取用户信息失败');
								}
							},
							fail: (err) => {
								reject(err);
							}
						});
					});
				});
			
				Promise.all(promises)
					.then(userInfos => {
						// 处理获取到的用户信息
						console.log(userInfos);
						// 这里可以更新界面或存储用户信息
					})
					.catch(error => {
						console.error('请求用户信息出错:', error);
					});
			}
		},
		mounted() {
			const value = uni.getStorageSync('nowAccount');

			// 提取 userId
			if (value && value.data) {
				this.user.userId = value.data.userId;
			}

			console.log(this.user.userId);
		}
	};
</script>

<style lang="scss">
	.u-demo-wrap {
		display: flex;
		align-items: center;
		background: #fff3f4;
	}

	.myinfo {
		display: flex;
		align-items: center;
		padding: 50rpx;
		background-image: linear-gradient(to top left, #ffcbcc, #ffe4e6);
	}

	.grid {
		background-color: #ffcbcc;
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
		align-items: center;

	}

	.u-avatar-wrap {
		margin-top: 80rpx;
		overflow: hidden;
		margin-bottom: 80rpx;
		text-align: center;
		margin-left: 299rpx;
	}



	.content {
		flex: 1;
		overflow-y: auto;
	}

	.list {
		display: flex;
		flex-direction: column;
		/* 垂直排列列表项 */
		background-color: #ffffff;
		flex: 1;
		/* 占据剩余空间 */
		overflow-y: auto;
		/* 启用垂直滚动 */
		border-bottom: 1px solid #f0dddf;
		background: #fff3f4;
	}

	.useravatar {
		width: 300rpx;
		height: 300rpx;
		border-radius: 10%;
		margin-right: 10px;
	}

	.item-title {
		font-size: 13px;
		color: #333;
	}
</style>