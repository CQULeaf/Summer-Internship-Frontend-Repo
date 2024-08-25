<template>
	<view>
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
				<u-tabs-swiper ref="uTabs" :list="pagelist" :current="pagecurrent" @change="tabsChange"
					:is-scroll="false" :bold="false" bg-color="#ffc7cb" active-color="#000000"></u-tabs-swiper>
			</view>
			<swiper class="swiper" :current="swiperCurrent" @transition="transition" @animationfinish="animationfinish">
				<swiper-item v-for="(tab, tabindex) in pagelist" :key="tabindex">
					<scroll-view class="scroll-view" scroll-y @scrolltolower="onreachBottom">
						<view class="list">
							<!-- 用户列表 -->
							<view v-if="pagelist[pagecurrent].type === 'post'">
								<view class="list-item" v-for="(item, index) in currentItems" :key="index"
									@click="goToPost(item)">
									<text class="item-title">{{item.title}}</text>
								</view>
							</view>
							<view v-if="pagelist[pagecurrent].type === 'liked'">
								<view class="list-item" v-for="(item, index) in currentItems" :key="index"
									@click="goToLiked(item)">
									<text class="item-title">{{item.title}}</text>
								</view>
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
					userId: ''
				},
				pic: 'https://uviewui.com/common/logo.png',
				show: true,
				// 定义一个变量
				logined: true,
				list: '',
				current: 4,
				pagelist: [
					// 标签数据
					{
						name: '帖子',
						type: 'post',
						api: 'http://127.0.0.1:4523/m1/5010181-4669608-default/ccPost/mypost'
					},
					{
						name: '点赞',
						type: 'liked',
						api: 'http://127.0.0.1:4523/m1/5010181-4669608-default/follow/getFollowedPosts'
					},
				],
				pagecurrent: 0, //变量名：变量值
				swiperCurrent: 0,
				currentItems: [], // 当前用户列表数据
				// user: {
				// 	avatar: '/static/image/1.png',
				// 	nickname: 'MyNickname'
				// },
				dataCache: {},
				loading: false,
				page: 1, // 当前页码
				hasMore: true, // 是否还有更多数据
				touchStartX: 0,
				touchEndX: 0,
				touchThreshold: 30 //处理滑动
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
			// const avatarData = uni.getStorageSync('avatarData')
			// this.user.avatar=avatarData
			// uni.removeStorageSync('avatarData')
			//console.log(this.user.avatar)
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
					url: 'http://localhost:1234/user/updateAvatar',
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
				// 跳转到聊天界面，假设聊天界面的路径为 '/pages/me/mypost'
				uni.navigateTo({
					url: '/pages/me/mypost?post_id=' + item.postId // 假设用户有一个 id 属性
				});
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
			onreachBottom() {
				if (this.loading || !this.hasMore) return; // 正在加载中或没有更多数据则不执行
				this.page++;
				this.fetchUserList(this.pagelist[this.pagecurrent].api, this.page);
			},
			//发送请求
			fetchUserList(apiEndpoint, page) {
				this.loading = true;
				uni.request({
					url: `${apiEndpoint}?page=${page}`, //在fetchUserList方法中的请求URL使用了'${apiEndpoint}?page=${page}'，这会导致字符串直接拼接而非动态生成变量。正确的做法应该是使用模板字符串的语法来动态替换变量
					method: 'GET',
					success: (res) => {
						console.log('请求成功:', res);
						console.log('返回的数据:', res.data.data);
						if (res.statusCode === 200) {
							if (res.data.length === 0) {
								this.hasMore = false; // 没有更多数据
							} else {
								if (this.pagelist[this.pagecurrent].type === 'post') {
									// 处理用户数据
									this.currentItems = [...this.currentItems, ...res.data.data];
								} else if (this.pagelist[this.pagecurrent].type === 'liked') {
									this.currentItems = [...this.currentItems, ...res.data.data];
									// 使用帖子 ID 请求帖子的其他信息
									this.fetchPostDetails(res.data.data.map(post => post.postId));
								}
								// 处理帖子数据
								this.dataCache[apiEndpoint] = this.currentItems;
							}
						} else {
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
			fetchPostDetails(postIds) {
			        const fetchPromises = postIds.map(postId => {
			            return new Promise((resolve, reject) => {
			                uni.request({
			                    url: `http://127.0.0.1:4523/m1/5010181-4669608-default/ccPost/getPost/${postId}`,
			                    method: 'GET',
			                    success: (res) => {
									console.log('返回的数据:', res.data.data);
			                        if (res.statusCode === 200) {
			                            resolve(res.data.data);
			                        } else {
			                            reject(new Error('获取帖子详情失败'));
			                        }
			                    },
			                    fail: (err) => {
			                        reject(err);
			                    }
			                });
			            });
			        });
			
			        Promise.all(fetchPromises)
			            .then(detailsArray => {
			                this.currentItems = this.currentItems.map(item => {
			                    const detail = detailsArray.find(post => post.postId === item.postId);
			                    return detail ? { ...item, ...detail } : item;
			                });
			            })
			            .catch(error => {
			                uni.showToast({ title: '详细信息请求失败', icon: 'none' });
			                console.error(error);
			            });
			    },
			handleTouchStart(e) {
				this.touchStartX = e.touches[0].clientX;
			},
			handleTouchEnd(e) {
				this.touchEndX = e.changedTouches[0].clientX;
				let swipeDistance = this.touchEndX - this.touchStartX;
				if (Math.abs(swipeDistance) > this.touchThreshold) {
					this.swiperCurrent = swipeDistance > 0 ? Math.max(0, this.swiperCurrent - 1) : Math.min(this.list
						.length - 1, this.swiperCurrent + 1);
				}
			},
			tabsChange(index) {
				this.swiperCurrent = index;
				this.pagecurrent = index;
				this.page = 1; // 重置页码
				this.hasMore = true; // 重新设置有更多数据标志
				this.currentItems = []; // 清空当前用户列表
				this.loading = true; // 开始加载
				this.fetchUserList(this.pagelist[index].api, this.page);
			},
			transition(e) {
				this.handleTouchEnd(e); // 处理滑动
				let dx = e.detail.dx;
				this.$refs.uTabs.setDx(dx);
			},

			animationfinish(e) {
				let pagecurrent = e.detail.pagecurrent;
				if (pagecurrent < 0) {
					pagecurrent = 0;
				} else if (pagecurrent >= this.pagelist.length) {
					pagecurrent = this.pagelist.length - 1;
				}
				this.$refs.uTabs.setFinishCurrent(pagecurrent);
				this.swiperCurrent = pagecurrent;
				this.pagecurrent = pagecurrent;

				if (!this.dataCache[this.pagelist[pagecurrent].api]) {
					this.loading = true; // 开始加载
					this.fetchUserList(this.pagelist[pagecurrent].api, this.page);
				} else {
					this.currentUsers = this.dataCache[this.pagelist[pagecurrent].api];
					this.loading = false; // 数据已缓存，无需再次加载
				}
			}

		},
		mounted() {
			this.fetchUserList(this.pagelist[this.pagecurrent].api, this.page);
		}
	};
</script>

<style lang="scss">
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
	}

	.u-avatar-wrap {
		margin-top: 80rpx;
		overflow: hidden;
		margin-bottom: 80rpx;
		text-align: center;
	}

	.list-item {
		display: flex;
		align-items: center;
		padding: 25px;
		border-bottom: 1px solid #f0dddf;
	}

	.item-title {
		font-size: 13px;
		color: #333;
	}
</style>