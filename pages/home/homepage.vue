<template>
	<view>
		<u-navbar :is-back="false" title="主页" :background="background" height="55" title-size=40 title-color=#262626>

		</u-navbar>
	
		<view class="search">
			<u-search placeholder="请输入标题关键字" v-model="keyword" @search="handleSearch" height="80" shape="round"
				bg-color="#ededed" input-align="center" margin="10px" @blur="handleSearch"></u-search>
		</view>
		<view class="wrap">
			<swiper class="swiper-box" :current="swiperCurrent" @transition="transition"
				@animationfinish="animationfinish">
				<swiper-item class="swiper-item">
					<scroll-view scroll-y style="height: 100%;width: 100%;">
						<view class="post" v-for="(res, index) in postList" :key="res.postId">
							<view class="right">
								<view class="content" @click="toReply(res)">{{ res.title }}</view>
								<view class="u-line-2" @click="toReply(res)">{{ res.postContent }}</view>
								<view v-if="res.cover">
									<u-image width="100%" height="300rpx" :src="res.cover"
										@tap="preAvatar(res.cover)"></u-image>
								</view>
								<view class="top">
									<view class="createdAt">{{ res.createdAt }}</view>
								</view>
							</view>
						</view>
					</scroll-view>
				</swiper-item>
				<swiper-item class="swiper-item">
					<scroll-view scroll-y style="height: 100%;width: 100%;">
						<view class="post" v-for="(res, index) in postList" :key="res.postId" @search="search"
							@custom="custom">
							<view class="right">
								<view class="content" @click="toReply(res)">{{ res.title }}</view>
								<view class="u-line-2" @click="toReply(res)">{{ res.postContent }}</view>
								<view v-if="res.cover">
									<u-image width="100%" height="300rpx" :src="res.cover"></u-image>
									<u-image width="100%" height="400rpx" :src="res.cover"></u-image>
								</view>
								<view class="like" :class="{ highlight: res.likeCount }">
									<view class="num">{{ res.likeCount }}</view>
									<u-icon v-if="!res.isLike" name="thumb-up" :size="30" color="#9a9a9a"
										@click="getLike(index)"></u-icon>
									<u-icon v-if="res.isLike" name="thumb-up-fill" :size="30"
										@click="getLike(index)"></u-icon>
								</view>
								<view class="top">
									<view class="nickname">{{ res.nickname }}</view>
									<view class="createdAt">{{ res.createdAt }}</view>
								</view>
							</view>
						</view>
					</scroll-view>
				</swiper-item>

			</swiper>
		</view>
		<u-tabbar v-model="current" :list="list" :mid-button="true"></u-tabbar>
	</view>
</template>

<script>
	export default {
		data() {
			return {

				// 背景颜色
				background: {
					backgroundColor: '#fed6dc'
				},
				list: '',
				current: 0,
				keyword: '',
				swiperCurrent: 0,
				dx: 0,
				homelist: [{
						name: ' 推荐'
					},
					{
						name: '关注',
					}
				],
				postList: [{
					userId: '',
					postId: '',
					nickname: '',
					createdAt: '',
					postContent: '',
					title: '',
					cover: '',
					likeCount: '',
					isLike: '0',
				}],
				originalPostList: '',


				keyword: '',
				user: [],
				originalUserList: [], // 添加这行来保存原始用户列表
			};
		},

		onLoad: function(options) {
			setTimeout(function() {
				console.log('start pulldown');
			}, 1000);
			uni.startPullDownRefresh();
		},
		onPullDownRefresh() {
				this.keyword='',
				// 请求帖子数据
				uni.request({
					url:'http://localhost:8080/ccPost/getAllPosts',
					success: (res) => {
						this.postList=res.data.data
						this.originalPostList = this.postList
					}
				})
				//请求用户数据
				uni.request({
					url:'http://localhost:8080/user/getUserByNickname',
					data:this.user,
					success:(res)=>{
						console.log(res)
						this.user=res.data.data
						this.originalPostList=this.user
					}
				})
				console.log('refresh');
				setTimeout(function () {
					uni.stopPullDownRefresh();
				}, 1000);
			},
	
		onShow() {

			this.keyword = '',
				// 请求帖子数据
				uni.request({
					url: 'http://localhost:8080/ccPost/getAllPosts',
					success: (res) => {
						this.postList = res.data.data
						this.originalPostList = this.postList
					}
				})
			//请求用户数据
			uni.request({
				url: 'http://localhost:8080/user/getUserByNickname',
				data: this.user,
				success: (res) => {
					console.log(res)
					this.user = res.data.data
					this.originalPostList = this.user
				}
			})

			this.list = [{
					iconPath: "/static/newhomeg.png",
					selectedIconPath: "/static/newhomep.png",
					text: '家',
					
					customIcon: false,
					pagePath: '/pages/home/homepage'
				},
				{
					iconPath: "/static/happygrey.png",
					selectedIconPath: "/static/happierp.png",
					text: '聚',
					
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

		methods: {
			handleSearch() {

				// 如果搜索关键字不为空，过滤帖子  
				if (this.keyword) {
					const filteredPosts = this.postList.filter(post => post.title.includes(this.keyword) || post
						.postContent.includes(this.keyword));
					this.postList = filteredPosts;
					const filteredUsers = this.user.filter(user => user.username.includes(this.keyword));
					// 您可以选择将结果存储在一个新的变量中，或者更新当前的用户视图  
					console.log(filteredUsers); // 显示搜索结果  
				} else {
					// 如果搜索关键字为空，重置帖子列表为原始数据
					this.postList = this.originalPostList;
				}
			},
			// 评论
			gotoCommentpage() {

			},

			preAvatar(img) {
				wx.previewImage({
					current: '', // 当前显示图片的 http 链接
					urls: [img] // 需要预览的图片 http 链接列表
				})
			},

			// 进入帖子的回复界面
			toReply(index) {
				uni.setStorageSync("postData", index)
				uni.navigateTo({
					url: '/pages/home/reply',

				})
			},

			custom() {

			},

			// 点赞
			getLike(index) {
				this.postList[index].isLike = !this.postList[index].isLike;
				if (this.postList[index].isLike == true) {
					this.postList[index].likeCount++;
				} else {
					this.postList[index].likeCount--;
				}
			},

			// tab栏切换
			change(index) {
				this.current = index; // 更新当前索引
				this.swiperCurrent = index; // 更新swiper的索引
			},
			transition({
				detail: {
					dx
				}
			}) {
				this.$refs.tabs.setDx(dx);
			},
			animationfinish({
				detail: {
					current
				}
			}) {
				this.$refs.tabs.setFinishCurrent(current);
				this.swiperCurrent = current;
				this.current = current;
			}
		}
	};
</script>





<style>
	/* #ifndef H5 */
	page {
		height: 100%;
		background-color: #f2f2f2;
	}

	/* #endif */
</style>

<style lang="scss" scoped>
	.like {
		display: flex;
		align-items: center;
		color: #9a9a9a;
		font-size: 26rpx;

		.num {
			margin-right: 4rpx;
			color: #9a9a9a;
		}
	}

	.wrap {
		display: flex;
		flex-direction: column;
		height: calc(100vh - var(--window-top));
		width: 100%;
		color: #646464;
	}

	.search {
		// border-bottom: 1px solid #a7a7a7;
		display: flex;
		background-color: #ffffff;
	}

	.swiper-box {
		flex: 1;
	}

	.swiper-item {
		height: 100%;
	}

	.post {
		border-bottom: 1px solid #ededed;
		background-color: #f9f9f9;
		text-align: left;
		display: flex;
		padding: 30rpx;

		.left {
			image {
				width: 64rpx;
				height: 64rpx;
				border-radius: 50%;
				background-color: #f2f2f2;
			}
		}

		.right {
			flex: 2;
			padding-left: 10rpx;
			font-size: 30rpx;

			.top {
				display: flex;
				justify-content: space-between;
				align-items: center;
				margin-bottom: 10rpx;

				.nickname {
					color: #c8c9cc;
				}

				.createdAt {
					color: #c8c9cc;
				}
			}

			.content {
				margin-bottom: 30rpx;
				margin-top: 30rpx;
				font-size: 25px;
				font-weight: bold;

			}

			.text {
				margin-top: 30rpx;
				margin-bottom: 20rpx;
				display: flex;
				font-size: 24rpx;
				color: #9a9a9a;
			}
		}
	}
</style>