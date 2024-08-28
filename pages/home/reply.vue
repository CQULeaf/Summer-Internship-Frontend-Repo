<template>

	<view class="wrap">
		<u-navbar :is-back="true" title="帖子" :background="background" :customBack="backtohome" height="55"  title-size=40>
		</u-navbar>
		<view class="comment">
			<view class="top">
				<view class="left" @click="gotoUserPage">
					<view class="heart-photo">
						<image :src="user.avatar" mode=""></image>
					</view>
					<view class="user-info">
						<view class="name">{{ user.nickname }}</view>
						<view class="date">{{comment.createdAt}}</view>
					</view>
				</view>
			</view>
			<view class="post">
				<view class="content u-font-40">{{ comment.title}}</view>
				<view class="content u-p-t-30">{{ comment.postContent }}</view>
				<image v-if="comment.cover" :src="comment.cover" mode=""></image>
			</view>
		</view>

		<view class="input-container">
			<input v-model="messageInput" placeholder="编辑你的内容..." />
			<button @click="sendMessage">发送</button>
		</view>

		<!-- 回复 -->
		<view class="all-reply">
			<view class="all-reply-top">全部回复</view>
			<view class="item" v-for="(item, index) in commentList" :key="index">
				<view class="comment">
					<view class="top">
						<view class="left">
							<view class="heart-photo" @click="gotoUserPage2(item)">
								<image :src="item.avatar" mode=""></image>
							</view>
							<view class="user-info">
								<view class="name">{{ item.nickname }}</view>
								<view class="date">{{ item.createdAt }}</view>
							</view>
						</view>

					</view>
					<view class="content">{{ item.content }}</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				background:
				 {
				
					// 背景颜色
				 	backgroundColor:"#ffc7cb"
				},
				user: {
					userId: '',
					avatar: '',
					nickname: ''
				},
				comment: {
					userId: '',
					nickname: '',
					date: '',
					postContent: '',
					title: '',
					cover: '',
					likeCount: '',
					isLike: '',
				},
				commentList: [{
					avatar: '',
					nickname: '',
					userId: ''
				}],
				addComment: {
					userId: 1,
					content: "This is a comment.",
					createdAt: "",
					updatedAt: "",
					deletedAt: "",
					likeCount: 0,
					postId: 2
				},
				messageInput: '',
			};
		},
		onLoad() {
			this.getComment();
			setTimeout(function() {
				console.log('start pulldown');
			}, 1000);
			uni.startPullDownRefresh();
		},

		onShow() {
			this.getComment();
			setTimeout(function() {
				console.log('start pulldown');
			}, 1000);
			uni.startPullDownRefresh();
		},
		

		onPullDownRefresh() {
				this.getComment()
				log('refresh');
				setTimeout(function () {
					uni.stopPullDownRefresh();
				}, 1000);
			},
	

		methods: {
			// 发送信息(评论)
			sendMessage() {
				if (this.messageInput == '') {
					this.$u.toast("请输入评论内容")
					return
				}
				uni.getStorage({
					key: "nowAccount",
					success: (res) => {
						this.addComment.userId = res.data.data.userId
						this.addComment.content = this.messageInput
						this.addComment.postId = this.comment.postId
						console.log(this.addComment);
						uni.request({
							url: "http://localhost:8080/comment/add",
							data: this.addComment,
							method: "POST",
							success(res) {

							}
						})
					}
				})

			},
			backtohome() {
				uni.switchTab({
					url: "/pages/home/homepage"
				})

			},
			// 获取所有的评论信息
			getComment() {
				uni.getStorage({
					key: 'postData',
					success: (res) => {
						this.comment = res.data;

						// 请求获得所有的回复数据
						uni.request({
							url: "http://localhost:8080/comment/getReply",
							data: res.data,
							success: (response) => {
								this.commentList = response.data.data;

								// 收集所有用户信息请求的 promises
								const userRequests = this.commentList.map((comment) => {
									return new Promise((resolve) => {
										uni.request({
											url: "http://localhost:8080/user/getUserInfo",
											data: comment,
											success: (res2) => {
												comment.avatar = res2
													.data.data.avatar;
												comment.nickname = res2
													.data.data
													.nickname;
												comment.userId = res2
													.data.data.userId;
												resolve();
											}
										});
									});
								});

								// 等待所有用户信息请求完成
								Promise.all(userRequests).then(() => {
									console.log("All user info loaded");
									// 此处可以更新视图或进行其他处理
								});
							}
						});
					}
				});

				uni.request({
					url: "http://localhost:8080/user/getUserInfo",
					data: this.comment,
					success: (res) => {
						this.user = res.data.data;
						uni.setStorage({
							key: "userPost",
							data: res.data.data
						});
					}
				});
			},

			//跳转到发帖人用户界面
			gotoUserPage() {
				uni.getStorage({
					key: "nowAccount",
					success: (res) => {
						if (res.data.data.userId != this.comment.userId) {
							uni.navigateTo({
								url: "/pages/userPage"
							})
						} else {
							uni.navigateTo({
								url: "/pages/me/myinfo/Information"
							})
						}
					},
				})
			},


			// 跳转到评论者用户界面页面
			gotoUserPage2(userId) {
				console.log(userId)
				uni.request({
					url: "http://localhost:8080/user/getUserInfo",
					data: userId,
					success: (res) => {
						console.log(userId);
						uni.setStorage({
							key: "userPost",
							data: res.data.data,
							success() {
								console.log(res);
								uni.getStorage({
									key: "nowAccount",
									success: (res) => {
										if (res.data.data.userId != res.userId) {
											uni.navigateTo({
												url: "/pages/userPage"
											})
										} else {
											uni.navigateTo({
												url: "/pages/me/myinfo/Information"
											})
										}
									},
								})
							}
						})
					},
				})
			},
		}
	};
</script>

<style lang="scss" scoped>
	page {
		background-color: #f2f2f2;
	}


	.input-container {
		background-color: #ffffff;
		display: flex;
		height: 80px;

	}

	input {

		flex: 1;
		padding: 10px;
		border: 1px solid #ddd;
		border-radius: 10px;
		margin-left: 10px;
		background: linear-gradient(45deg, #ffffff, #ffffff);
		margin-top: 20px;
	}

	button {
		padding: 1px 20px;
		border: none;
		border-radius: 20px;
		height: 40px;
		background: linear-gradient(45deg, #ffc7cd, #ffd8dc);
		color: #fff;
		cursor: pointer;
		margin-left: 10px;
		font-size: 16px;
		transition: background 0.3s, transform 0.3s;
		margin-top: 20px;
		margin-right: 10px;
	}

	.comment {
		padding: 30rpx;
		font-size: 32rpx;
		background-color: #ffffff;

		.top {
			display: flex;
			justify-content: space-between;
		}

		.left {
			display: flex;

			.heart-photo {
				image {
					width: 64rpx;
					height: 64rpx;
					border-radius: 50%;
					background-color: #f2f2f2;
				}
			}

			.user-info {
				margin-left: 10rpx;

				.name {
					color: #5677fc;
					font-size: 28rpx;
					margin-bottom: 4rpx;
				}

				.date {
					font-size: 20rpx;
					color: $u-light-color;
				}
			}
		}

		.right {
			display: flex;
			font-size: 20rpx;
			align-items: center;
			color: #9a9a9a;

			.like {
				margin-left: 6rpx;
			}

			.num {
				font-size: 26rpx;
				color: #9a9a9a;
			}
		}

		.highlight {
			color: #5677fc;

			.num {
				color: #5677fc;
			}
		}
	}

	.all-reply {
		margin-top: 10rpx;
		padding-top: 20rpx;
		background-color: #ffffff;

		.all-reply-top {
			margin-left: 20rpx;
			padding-left: 20rpx;
			border-left: solid 4rpx #5677fc;
			font-size: 30rpx;
			font-weight: bold;
		}

		.item {
			border-bottom: solid 2rpx $u-border-color;
		}
	}
</style>