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
			<swiper class="swiper" :current="swiperCurrent">
				<swiper-item v-for="(tab, tabindex) in pagelist" :key="tabindex">
					<scroll-view class="scroll-view" scroll-y @scrolltolower="onreachBottom">
						<view class="list">
							<!-- 用户发布的帖子列表 -->
							<view v-if="pagelist[pagecurrent].type === 'post'">
								<view class="list-item" v-for="(item, index) in postList" :key="index" @click="goToPost(item)">
									<text class="item-title">{{item.title}}</text>
								</view>
								<u-divider>完了...</u-divider>
							</view>
							
							<!-- 用户的点赞列表 -->
							<view v-if="pagelist[pagecurrent].type === 'liked'">222
							
							<u-divider>完了...</u-divider>
							</view>
						</view>
					</scroll-view>
				</swiper-item>
			</swiper>
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
					userId:1,
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
				pagecurrent: 0, //变量名：变量值
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
					title:''
				}], //发布帖子的数据
				
				likeList:[{
					
				}]
			};
		},
		onLoad() {
			const value = uni.getStorageSync('nowAccount');
			this.user = value.data
			this.logined = (value.code == 200)
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
				key:'nowAccount',
				success: (ret) => {
					//console.log(ret.data.data.userId);
					this.user = ret.data.data
					
					//判断用户是否登录
					this.logined = (ret.data.code == 200)
					console.log(this.user);
					// //获取用户帖子信息
					uni.request({
						url:"http://localhost:1234/ccPost/mypost",
						data: {
						    user_id: ret.data.data.userId
						},
						success:(res)=>{
							console.log(res.data.data);
							this.postList=res.data.data
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
			goToPost(item) {
				// 跳转到聊天界面，假设聊天界面的路径为 '/pages/home/reply'
				uni.setStorage({
					key:"postData",
					data:item,
					success() {
						uni.navigateTo({
							url:"/pages/home/reply"
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
			tabsChange(index) {
				this.swiperCurrent = index;
				this.pagecurrent = index;
				this.page = 1; // 重置页码
				this.hasMore = true; // 重新设置有更多数据标志
				this.currentItems = []; // 清空当前用户列表
				this.loading = true; // 开始加载
			},
		},
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