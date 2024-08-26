<template>
	<view class="container">
		<view>
			<u-navbar height=60 back-text="" title="" :background="background" is-back=true :customBack="gotopofile">
				<view class="slot-wrap"><!-- 通过自定义slot传入的内容 -->
					<u-tabs :list="list" :is-scroll="false" :current="current" @change="change" active-color="#000000"
						inactive-color="#606266" item-width=140 bg-color="#ffc7cb"></u-tabs>
				</view>
			</u-navbar>
		</view>
		<view v-if="current === 0" class="list">
			<view v-if="info.length === 0" class="loading">加载中...</view>
			<view v-for="(friend, index) in info" :key="index" class="list-item">
				<image class="useravatar" :src="friend.avatar" />
				<text class="nickname">{{ friend.username }}</text>
			</view>
		</view>
		<view v-else-if="current===1" class="list">
			<view v-if="follows.length === 0" class="loading">加载中...</view>
			<view v-for="(follow, index) in follows" :key="index" class="list-item">
				<image class="useravatar" :src="follow.avatar" />
				<text class="nickname">{{ follow.username }}</text>
			</view>
		</view>
		<view v-else-if="current===2" class="list">
			<view v-if="fans.length === 0" class="loading">加载中...</view>
			<view v-for="(fan, index) in fans" :key="index" class="list-item">
				<image class="useravatar" :src="fan.avatar" />
				<text class="nickname">{{ fan.username }}</text>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return { //专门写变量   在模板里面写 ：herf 表示herf是变量
				background: {
					backgroundColor: '#ffc7cb',
				}, //导航栏的颜色
				list: [
					// 标签数据
					{
						name: '朋友',

					},
					{
						name: '关注',

					},
					{
						name: '粉丝',

					}
				],
				current: 0,
				user: {
					avatar: '',
					username: '',
					userId: ''
				},
				friends: [],
				follows: [],
				fans: [],
				info: [],
			};
		},

		methods: { //写自定义方法
			gotopofile() {
				uni.switchTab({
					url: '/pages/me/mypage' // 返回上一页面
				});
			},
			change(index) {
				this.current = index;
				if (index === 0) { // 如果点击的是“朋友”
					this.getfriends(); // 获取朋友列表
				} else if (index === 1) {
					this.getfollows();
				} else if (index === 2) {
					this.getfans();
				}
			},
			getfriends() {
				uni.getStorage({
					key: 'nowAccount',
					success: (res) => {
						this.user.userId = res.data.data.userId,
							console.log('获取到的 userId:', this.user.userId); // 打印 userId
						uni.request({
							url: `http://localhost:8080/user/friends?userId=${this.user.userId}`,
							// data: this.user.userId, 请求体
							method: 'GET',
							success: (res) => {
								console.log(res)
								if (res.statusCode === 200) {
									this.friends = res.data.data
									console.log('用户列表:', this.friends)
									this.fetchUserInfos(this.friends);
								} else {
									console.error('获取朋友列表失败:', res);
								}
							},
							fail: (err) => {
								console.error('请求失败:', err);
							}
						})
					}
				})

			},
			getfollows() {
				uni.request({
					url: 'http://127.0.0.1:4523/m1/5010181-4669608-default/follow/friends1',
					data: this.user,
					method: 'GET',
					success: (res) => {
						console.log(res)
						if (res.statusCode === 200) {
							this.follows = res.data.data
						} else {
							console.error('获取关注列表失败:', res);
						}
					},
					fail: (err) => {
						console.error('请求失败:', err);
					}
				})
			},
			getfans() {
				uni.request({
					url: 'http://127.0.0.1:4523/m1/5010181-4669608-default/follow/friends1',
					data: this.user,
					method: 'GET',
					success: (res) => {
						console.log(res)
						if (res.statusCode === 200) {
							this.fans = res.data.data
						} else {
							console.error('获取粉丝列表失败:', res);
						}
					},
					fail: (err) => {
						console.error('请求失败:', err);
					}
				})
			},
			fetchUserInfos(users) {
				console.log('获取到的 users', users);
				console.log('获取到的 users', users.length);
				// 打印 userId
				for (let i = 0; i < users.length; i++) {
					const userId = users.user_id; // 获取每个用户的 user_id
					console.log('获取到的 userId:', userId);
					uni.request({
						url: `http://localhost:8080/user/getUserInfo`,
						data:{
							userId:userId
						},
						method: 'GET',
						success: (res) => {
							console.log('获取到的 info:', res)
							if (res.statusCode === 200) {
								this.info = res.data.data
							} else {
								console.error('获取失败:', res);
							}
						},
						fail: (err) => {
							console.error('请求失败:', err);
						}
					})
				}
			},
		},
	}
</script>
<style>
	.container {
		display: flex;
		flex-direction: column;
		height: 100vh;
		/* 占满视口高度 */
	}

	.slot-wrap {
		padding: 0 90rpx;
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
		font-size: 28rpx;
		/* 示例字体大小 */
		color: #333;
		/* 示例字体颜色 */
	}


	.loading {
		text-align: center;
		padding: 20px;
		font-size: 16px;
		color: #888;
	}
</style>