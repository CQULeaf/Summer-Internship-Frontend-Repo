<template>
	<view class="container">
		<view>
			<u-tabs-swiper ref="uTabs" :list="list" :current="current" @change="tabsChange" :is-scroll="false"
				swiper-Width="100" :bold="false" active-color="#ffa4ad"></u-tabs-swiper>
		</view>

		<!-- 用户列表部分 -->
		<swiper class="swiper" :current="swiperCurrent" @transition="transition" @animationfinish="animationfinish">
			<swiper-item v-for="(tab, tabindex) in list" :key="tabindex">
				<scroll-view class="scroll-view" scroll-y @scrolltolower="onreachBottom">
					<view class="list">
						<!-- 用户列表 -->
						<view v-if="list[current].type === 'users'">
							<view class="list-item" v-for="(item, index) in currentItems" :key="index">
								<image class="useravatar" :src="item.avatar"></image>
								<text class="item-title">{{item.nickname}}</text>
							</view>
						</view>

						<!-- 帖子列表 -->
						<view v-if="list[current].type === 'posts'">
							<view class="list-item" v-for="(post, index) in currentItems" :key="index">
								<text class="post-title">{{post.title}}</text>
							</view>
						</view>
					</view>
				</scroll-view>
			</swiper-item>
		</swiper>
	</view>
</template>

<script>
	export default {
		data() {
			return { //专门写变量   在模板里面写 ：herf 表示herf是变量
				list: [
					// 标签数据
					{
						name: '朋友',
						type: 'users',
						api: 'http://127.0.0.1:4523/m1/5010181-4669608-default/user/friends'
					},
					{
						name: '关注',
						type: 'users',
						api: 'http://127.0.0.1:4523/m1/5010181-4669608-default/user/follwing'
					},
					{
						name: '粉丝',
						type: 'users',
						api: 'http://127.0.0.1:4523/m1/5010181-4669608-default/user/fans'
					},
					{
						name: '点赞',
						type: 'posts',
						api: 'http://127.0.0.1:4523/m1/5010181-4669608-default/user/thumb'
					},
					{
						name: '浏览记录',
						type: 'posts',
						api: 'http://127.0.0.1:4523/m1/5010181-4669608-default/user/browse'
					}
				],
				current: 0, //变量名：变量值
				swiperCurrent: 0,
				currentItems: [], // 当前用户列表数据
				user: {
					avatar: '/static/image/1.png',
					nickname: 'MyNickname'
				},
				dataCache: {},
				loading: false,
				page: 1, // 当前页码
				hasMore: true, // 是否还有更多数据
				touchStartX: 0,
				touchEndX: 0,
				touchThreshold: 30 //处理滑动
			};
		},

		methods: { //写自定义方法
			goToProfile() {
				uni.navigateTo({
					url: '/pages/me/setting'
				});
			},
			//    goToPost(postId){
			// 	uni.navigateTo({
			// 		url:'/'
			// 	})
			// },
			onreachBottom() {
				if (this.loading || !this.hasMore) return; // 正在加载中或没有更多数据则不执行
				this.page++;
				this.fetchUserList(this.list[this.current].api, this.page);
			},
			//发送请求
			fetchUserList(apiEndpoint, page) {
				this.loading = true;
				uni.request({
					url: `${apiEndpoint}?page=${page}`, //在fetchUserList方法中的请求URL使用了'${apiEndpoint}?page=${page}'，这会导致字符串直接拼接而非动态生成变量。正确的做法应该是使用模板字符串的语法来动态替换变量
					method: 'GET',
					success: (res) => {
						if (res.statusCode === 200) {
							if (res.data.length === 0) {
								this.hasMore = false; // 没有更多数据
							} else {
								if (this.list[this.current].type === 'users') {
									// 处理用户数据
									this.currentItems = [...this.currentItems, ...res.data];
								} else if (this.list[this.current].type === 'posts') {
									// 处理帖子数据
									this.currentItems = [...this.currentItems, ...res.data];
								}
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
				this.current = index;
				this.page = 1; // 重置页码
				this.hasMore = true; // 重新设置有更多数据标志
				this.currentItems = []; // 清空当前用户列表
				this.loading = true; // 开始加载
				this.fetchUserList(this.list[index].api, this.page);
			},


			transition(e) {
				this.handleTouchEnd(e); // 处理滑动
				let dx = e.detail.dx;
				this.$refs.uTabs.setDx(dx);
			},

			animationfinish(e) {
				let current = e.detail.current;
				if (current < 0) {
					current = 0;
				} else if (current >= this.list.length) {
					current = this.list.length - 1;
				}
				this.$refs.uTabs.setFinishCurrent(current);
				this.swiperCurrent = current;
				this.current = current;

				if (!this.dataCache[this.list[current].api]) {
					this.loading = true; // 开始加载
					this.fetchUserList(this.list[current].api, this.page);
				} else {
					this.currentUsers = this.dataCache[this.list[current].api];
					this.loading = false; // 数据已缓存，无需再次加载
				}
			}

		},
		mounted() {
			this.fetchUserList(this.list[this.current].api, this.page);
		}
	};
</script>
<style>
	.container {
		display: flex;
		flex-direction: column;
		height: 100vh;
		/* 占满视口高度 */
	}

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

	.swiper {
		flex: 1;
		/* 占据剩余的空间 */
	}

	.swiper-item {
		height: 100%;
		/* 确保 swiper-item 填满父容器 */
		background-color: #ffffff;
	}

	.scroll-view {
		width: 100%;
		height: 100%;
		/* 确保 scroll-view 填满 swiper-item */
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
	}

	.list-item {
		display: flex;
		align-items: center;
		padding: 10px;
	}

	.useravatar {
		width: 100rpx;
		height: 100rpx;
		border-radius: 50%;
		margin-right: 10px;
	}

	.nickname {
		font-size: 16px;
		color: #333;
	}

	.loading {
		text-align: center;
		padding: 20px;
		font-size: 16px;
		color: #888;
	}
</style>