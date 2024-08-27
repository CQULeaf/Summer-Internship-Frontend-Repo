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
			<view v-if="userlist.length === 0" class="loading">还没有朋友喔...</view>
			<view v-for="(friend, index) in userlist" :key="index" class="list-item">
					<image class="useravatar" :src="friend.avatar" @click="gotouserPage(friend)"/>
					<text class="nickname" @click="gotouserPage(friend)">{{ friend.nickname }}</text>
			</view>
		</view>
		<view v-else-if="current===1" class="list">
			<view v-if="userlist.length === 0" class="loading">还没有关注的人喔...</view>
			<view v-for="(follow, index) in userlist" :key="index" class="list-item">
					<image class="useravatar" :src="follow.avatar" @click="gotouserPage(follow)"/>
					<text class="nickname" @click="gotouserPage(follow)">{{ follow.nickname }}</text>
			</view>
		</view>
		<view v-else-if="current===2" class="list">
			<view v-if="userlist.length === 0" class="loading">还没有粉丝喔...</view>
			<view v-for="(fan, index) in userlist" :key="index" class="list-item">
				<image class="useravatar" :src="fan.avatar" @click="gotouserPage(fan)"/>
				<text class="nickname" @click="gotouserPage(fan)">{{ fan.nickname }}</text>
			</view>
		</view>
	</view>
</template>

<script>
import { type } from 'os';
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
				current: '',
				type:'',
				user: {
					avatar: '',
					username: '',
					userId: ''
				},
				friends: [],
				follows: [],
				fans: [],
				userlist: []
			};
		},
		methods: { //写自定义方法
			gotouserPage(index){
				console.log(index.userId);
				uni.setStorage({
					key:"userPost",
					data:index,
					success() {
						uni.navigateTo({
							url:"/pages/userPage"
						})
					}
				})
			},
		
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
									this.fetchUserInfos(this.friends,0);
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
			
			//获取关注我的人
			getfans() {
				uni.getStorage({
					key: 'nowAccount',
					success: (res) => {
						this.user.userId = res.data.data.userId,
						//console.log('获取到的 userId:', this.user.userId); // 打印 userId
						uni.request({
							url: `http://localhost:8080/user/followers?userId=${this.user.userId}`,
							// data: this.user.userId, 请求体
							method: 'GET',
							success: (res) => {
								console.log(res)
								if (res.statusCode === 200) {
									this.follows = res.data.data
									console.log('用户列表:', this.follows)
									this.fetchUserInfos(this.follows,1);
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
			
			//获取我关注的人
			getfollows() {
				uni.getStorage({
					key: 'nowAccount',
					success: (res) => {
						this.user.userId = res.data.data.userId,
						console.log('获取到的 userId:', this.user.userId,2); // 打印 userId
						uni.request({
							url: `http://localhost:1234/user/following?userId=${this.user.userId}&followableType=user`,
							// data: this.user.userId, 请求体
							method: 'GET',
							success: (res) => {
								console.log(res)
								if (res.statusCode === 200) {
									this.fans = res.data.data
									console.log('用户列表:', this.fans)
									this.fetchUserInfos(this.fans,2);
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
			fetchUserInfos(users,index) { // users 朋友的信息-id  
				//console.log('获取到的 users:', users);
				//console.log('获取到的长度:', users.length);
				console.log(index);
				var userIds=[];
				if(index===2){
					userIds = users.map(user => user.followableId); 
				}else if(index===1){
					userIds = users.map(user => user.userId); 
				}else{
					userIds = users.map(user => user.user_id); 
				}
				console.log('提取的用户ID:', userIds); // 输出提取的 userId 列表 

				const userRequests = [];

				userIds.forEach(userId => {
					//console.log('请求的用户ID:', userId); // 打印用户 ID
					if (userId) { // 确保 userId 不为空  
						userRequests.push(new Promise((resolve, reject) => {
							uni.request({
								url: `http://localhost:8080/user/getUserInfo?userId=${userId}`,
								method: 'GET',
								success: (res) => {
									//console.log('完整的响应:', res);
									if (res.statusCode === 200) {
										if (res.data && res.data.data) {
											console.log('返回的数据:', res.data.data);
											resolve(res.data.data);
										} else {
											reject('返回的数据格式不正确');
										}
									} else {
										reject(`获取用户信息失败: ${res.statusCode}`);
									}
								},
								fail: (err) => {
									reject('请求失败');
								}
							});
						}));
					} else {
						console.error('无效的用户ID:', userId); // 处理无效的 userId  
					}
				});

				Promise.all(userRequests)
					.then(userInfos => {
						this.userlist = userInfos;
						console.log(1111);
						console.log('当前用户列表:', this.userlist);
					})
					.catch(error => {
						console.error(error);
						uni.showToast({
							title: '获取部分用户信息失败',
							icon: 'none'
						});
					});
			},
		},
		mounted() {  
				const type = this.$route.query.type; // 获取URL参数  
				if (type === 'friends') {  
					this.current = 0;  
					this.getfriends(); // 获取朋友列表  
				} else if (type === 'follows') {  
					this.current = 1;  
					this.getfollows(); // 获取关注列表  
				} else if (type === 'fans') {  
					this.current = 2;  
					this.getfans(); // 获取粉丝列表  
				}  
			}  
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