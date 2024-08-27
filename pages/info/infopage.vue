<template>
	<view>
		<view class="container">
			<view>
				<u-navbar class="wrap" height=60 title="" :background="background" :is-back=false>
					<view class="subwrap">
						<u-tabs-swiper font-size="40" ref="uTabs" :list="pagelist" :is-scroll="false" swiper-Width="100"
							height=100 :bold="false" bg-color="rgba(255, 255, 255, 0)"
							active-color="#000000"></u-tabs-swiper>
					</view>
					<view>
						<text class="subwarp1" @click="goToPageTreeCave">树洞</text>
					</view>
				</u-navbar>
			</view>
			<!-- 用户列表部分 -->
			<swiper class="swiper">
				<swiper-item v-for="(tab, tabindex) in pagelist" :key="tabindex">
					<scroll-view class="scroll-view" scroll-y @scrolltolower="onreachBottom">
						<view class="subinfolist" @click="gotopofile(index)" v-for="(subinfo, index) in subinfolist"
							:key="index">
							<image class="image" :src="subinfo.image"></image>
							<text class="text">{{ subinfo.text }}</text>
						</view>
						<view class="list">
							<!-- 用户列表 -->
							<view v-if="pagelist[pagecurrent].type === 'msg'">
								<view class="list-item" v-for="(item, index) in currentItems" :key="index"
									@click="goToChat(item)">
									<image class="useravatar" :src="item.avatar"></image>
									<view class="usermessage">
										<text class="username">{{item.nickname}}</text>
										<text class="content">{{item.content}}</text>
										<text class="time">{{item.createdAt}}</text>
									</view>

								</view>
							</view>
						</view>
					</scroll-view>
				</swiper-item>
			</swiper>
		</view>
		<!-- 与包裹页面所有内容的元素u-page同级，且在它的下方 -->
		<u-tabbar v-model="current" :list="list" :mid-button="true"></u-tabbar>
		<text>点击进入树洞</text>
	</view>

</template>

<script>
	export default {
		data() {
			return { //专门写变量   在模板里面写 ：herf 表示herf是变量
				background: {
					backgroundImage: 'linear-gradient(45deg, rgb(255, 217, 220),rgb(255, 193, 199),rgb(255, 193, 199),rgb(255, 188, 195),rgb(159, 209, 177),rgb(112, 215, 164))',
					// 导航栏背景图
					background: 'url(static/navigatorbackground1.jpg) no-repeat',
					backgroundSize: 'cover',
				}, //导航栏的颜色
				list: '',
				current: 4,
				minUserListLength: 5,
				pagelist: [
					// 标签数据
					{
						name: '消息',
						type: 'msg',
						api: 'http://127.0.0.1:8080/message/history'
					}
				],
				subinfolist: [
					// 标签数据
					{
						image: '../../static/liked.png',
						text: '点赞',
					},
					{
						image: '../../static/comment.svg',
						text: '评论',
					},
					{
						image: '../../static/announcement.svg',
						text: '公告'
					}
				],
				pagecurrent: 0, //变量名：变量值
				swiperCurrent: 0,
				currentItems: [], // 当前用户列表数据
				dataCache: {},
				loading: false,
				page: 1, // 当前页码
				hasMore: true, // 是否还有更多数
				user: [],
				userlist: [],
				userlist1: []
			};
		},
		onLoad() {
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
		methods: { //写自定义方法
			goToPageTreeCave() {
				uni.navigateTo({
					url: "/pages/info/treecave/treecave"
				})
			},
			gotopofile(index) {
				// 根据 index 跳转到不同的页面
				switch (index) {
					case 0:
						// 跳转到点赞页面
						uni.navigateTo({
							url: "/pages/info/msg/like"
						})
						break;
					case 1:
						// 跳转到评论页面
						uni.navigateTo({
							url: "/pages/info/msg/comment"
						})
						break;
					case 2:
						// 跳转到公告页面
						uni.navigateTo({
							url: "/pages/info/msg/bulletin/bulletin"
						})
						break;
					default:
						break;
				}
			},
			goToChat(item) {
				uni.navigateTo({
					url: `/pages/info/infotalk?userId=${item.userId}` // 修改为你聊天页面的实际路径
				});
			},
			onreachBottom() {
				if (this.loading || !this.hasMore) return; // 正在加载中或没有更多数据则不执行
				this.page++;
				this.fetchUserList();
			},
			//发送请求
			fetchUserList() {
				if (!this.user.userId) {
					console.warn('userId is not set');
					return;
				}
				const pageSize = 3;
				uni.request({
					url: `http://localhost:1234/message/history?userId=${this.user.userId}`,
					method: 'GET',
					success: (res) => {
						console.log('请求成功:', res);
						if (res.statusCode === 200) {
							if (res.data.data.length === 0) {
								this.hasMore = false;
							} else {
								 this.hasMore = res.data.data.length === pageSize;
								if (this.pagelist[this.pagecurrent].type === 'msg') {
									this.userlist = res.data.data;
									console.log(this.userlist.length);

									const processedUserIds = new Set(); // 使用 Set 来跟踪已处理的用户 ID  
									const userRequests = []; // 存储所有用户请求的数组  
									const contentMap = new Map(); // 使用 Map 来存储 content 和 createdAt  

									// 遍历用户列表，提取 senderId 和 receiverId  
									this.userlist.forEach(user => {
										const senderId = user.senderId;
										const receiverId = user.receiverId;
										const content = user.content;
										const createdAt = user.createdAt;

										// 将 content 和 createdAt 存储到 Map 中，以便后续使用  
										const userKey = senderId === this.user.userId ? receiverId :
											senderId;
										contentMap.set(userKey, {
											content,
											createdAt
										});

										// 添加对方的 ID  
										processedUserIds.add(userKey);
									});

									// 遍历去重后的用户 ID，发起请求  
									processedUserIds.forEach(userId => {
										userRequests.push(new Promise((resolve, reject) => {
											uni.request({
												url: `http://localhost:1234/user/getUserInfo?userId=${userId}`,
												method: 'GET',
												success: (res) => {
													console.log('返回的数据:', res
														.data);
													console.log('用户ID:',
														userId);


													if (res.statusCode ===
														200) {
														resolve(res.data.data); // 解析成功数据  
													} else {
														reject(
															`获取用户信息失败: ${res.statusCode}`
															);
													}
												},
												fail: (err) => {
													reject('请求失败');
												}
											});
										}));
									});

									// 等待所有用户请求完成  
								Promise.all(userRequests)  
								    .then(userInfos => {  
								        console.log('所有用户信息:', userInfos); // 打印所有用户信息  
								        const uniqueUserInfos = new Map();  
								        
								        userInfos.forEach(userInfo => {  
								            console.log('用户信息:', userInfo); // 打印每个用户信息  
								            if (userInfo && userInfo.userId) {  
								                console.log('有效用户信息:', userInfo);  
								                const userContent = contentMap.get(userInfo.userId) || {};  
								                uniqueUserInfos.set(userInfo.userId, {  
								                    ...userInfo,  
								                    ...userContent  
								                });  
								            } else {  
								                console.warn('无效的用户信息或用户不存在:', userInfo);  
								            }  
								        });  
								
								        console.log('唯一用户信息数量:', uniqueUserInfos.size); // 打印唯一用户数量  
								        
								        // 将新用户合并到 currentItems  
								        this.currentItems = [...this.currentItems, ...Array.from(uniqueUserInfos.values())];  
								        console.log('合并后的用户列表:', this.currentItems); // 打印合并后的用户列表  
								        
								        uni.setStorage({  
								            key: 'usermessage',  
								            data: this.currentItems  
								        });  
								    })  
								    .catch(error => {  
								        console.error('请求失败:', error);  
								        uni.showToast({  
								            title: '获取部分用户信息失败',  
								            icon: 'none'  
								        });  
								    });
								}
							}
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
			loadUserIdAndFetch() {
				uni.getStorage({
					key: 'nowAccount',
					success: (res) => {
						this.user.userId = res.data.data.userId;
						console.log('获取到的 userId:', this.user.userId);
						this.fetchUserList(); // 在获取到 userId 后再调用 fetchUserList
					},
					fail: (err) => {
						uni.showToast({
							title: '无法获取用户信息',
							icon: 'none'
						});
					}
				});
			}
		},
		mounted() {
			this.loadUserIdAndFetch();
		}
	};
</script>
<style>
	.container {
		display: flex;
		flex-direction: column;
		height: 100vh;
		/* 占满视口高度 */
		background-color: #fffbfd;
	}

	.swiper {
		flex: 1;
		/* 占据剩余的空间 */
	}

	.swiper-item {
		height: 100%;
		/* 确保 swiper-item 填满父容器 */
	}

	.scroll-view {
		width: 100%;
		height: 100%;
		/* 确保 scroll-view 填满 swiper-item */
		background-color: #fffdff;
	}


	.list {
		display: flex;
		flex-direction: column;
		/* 垂直排列列表项 */
		background-color: #fffbfd;
		flex: 1;
		/* 占据剩余空间 */
		overflow-y: auto;
		/* 启用垂直滚动 */
	}

	.list-item {
		display: flex;
		align-items: center;
		padding: 10px;
		border-bottom: 1px solid #f0dddf;
		/* 使用 Flexbox 布局 */

	}

	.usermessage {
		display: flex;
		flex-direction: column;
		/* 垂直方向排列子元素 */
		align-items: flex-start;
		/* 左对齐 */
		margin-bottom: 10px;
		
	}

	.wrap {
		display: flex;
		/* 使用 flex 布局 */
		align-items: center;
		/* 水平居中 */
		padding: 0 110rpx;
		/* 背景颜色 */
		border: 2px solid #fcaea7;
		/* 边框 */
		border-radius: 5px;
		/* 圆角 */
		box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
		/* 阴影效果 */
	}

	.subwrap {
		margin-left: 120px;
	}

	.subwarp1 {
		margin-left: 20px;
		font-size: 20px;
	}

	.itemimage {
		width: 3px;
		height: 30px;
	}

	.subinfolist {
		display: flex;
		align-items: center;
		padding: 10px;
		background-color: #fffbfd;
		border-bottom: 1px solid #f0dddf;
		/* 2px 是边框宽度 */

	}

	.image {
		width: 100rpx;
		height: 100rpx;
		border-radius: 10%;
		margin-top: 3px;
		margin-right: 20px;
		margin-left: 10px;
	}

	.text {
		font-size: 16px;
		color: #333;
	}

	.useravatar {
		width: 120rpx;
		height: 120rpx;
		border-radius: 50%;
		margin-top: 3px;
		margin-right: 20px;
		margin-left: 10px;
	}

	.username {
		font-size: 19px;
		color: #333;
		margin-bottom: 8px;
		font-weight: bold;
	}

	.content {
		font-size: 17px;
		color: #333;
	}

	.time {
		font-size: 4px;
		color: #333;

	}

	.loading {
		text-align: center;
		padding: 20px;
		font-size: 16px;
		color: #888;
	}
</style>