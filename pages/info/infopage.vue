<template>
	<view>
		<view class="container">
			<!-- 用户信息部分 -->
			<!-- <view class="myinfo">
		  <image class="myavatar" :src="user.avatar"></image>
		  <text class="mynickname">{{ user.nickname }}</text>
		  <button size="default" style="background-color:#ffb9c0;color: #000000" @click="goToProfile">去设置</button>
		       </view> -->
			<!-- 切换标签部分 -->
			<view>
		
				<u-tabs-swiper font-size="40" ref="uTabs" :list="pagelist" :current="pagecurrent" @change="tabsChange"
					:is-scroll="false" swiper-Width="100" height=100 :bold="false" bg-color="#ffc7cb"
					active-color="#000000"></u-tabs-swiper>
		
		
			</view>
		
			<!-- 用户列表部分 -->
			<swiper class="swiper" :current="swiperCurrent" @transition="transition"
				@animationfinish="animationfinish">
				<swiper-item v-for="(tab, tabindex) in pagelist" :key="tabindex">
					<scroll-view class="scroll-view" scroll-y>
						<view class="subinfolist" @click="gotopofile(index)" v-for="(subinfo, index) in subinfolist"
							:key="index">
							<image class="image" :src="subinfo.image"></image>
							<text class="text">{{ subinfo.text }}</text>
						</view>
						<!-- <view class="Likeimage" @click="gotoLike">
							<image src='../../static/liked.png' class="action-icon"></image>
							<text class="Liketext">点赞</text>
						</view>
						<view class="Commentimage" @click="gotoComment">
							<image src='../../static/comment.svg' class="action-icon"></image>
							<text class="action-text">评论</text>
						</view>
						<view class="Bulletinimage" @click="gotoBulletin">
							<image src='../../static/announcement.svg' class="action-icon"></image>
							<text class="action-text">公告</text>
						</view> -->
					</scroll-view>
					<scroll-view class="scroll-view" scroll-y @scrolltolower="onreachBottom">
		
						<view class="list">
							<!-- 用户列表 -->
							<view v-if="pagelist[pagecurrent].type === 'msg'">
								<view class="list-item" v-for="(item, index) in currentItems" :key="index">
									<image class="useravatar" :src="item.avatar"></image>
									<text class="item-title">{{item.username}}</text>
								</view>
							</view>
		
							<!-- 帖子列表 -->
							<view v-if="pagelist[pagecurrent].type === 'treehole'" @click="treecave">
							</view>
						</view>
					</scroll-view>
				</swiper-item>
			</swiper>
		</view>
		<!-- 与包裹页面所有内容的元素u-page同级，且在它的下方 -->
		<u-tabbar v-model="current" :list="list" :mid-button="true"></u-tabbar>
	</view>

</template>

<script>
	export default {
		data() {
			return { //专门写变量   在模板里面写 ：herf 表示herf是变量
				// background: {
				// 	backgroundColor: '#ffc7cb',
				// }, //导航栏的颜色
				list: '',
				current: 4,
				pagelist: [
					// 标签数据
					{
						name: '消息',
						type: 'msg',
						api: 'http://127.0.0.1:4523/m1/5010181-4669608-default/info/message'
					},
					{
						name: '树洞',
						type: 'treehole'
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
				hasMore: true, // 是否还有更多数据
				touchStartX: 0,
				touchEndX: 0,
				touchThreshold: 30 //处理滑动
			};
		},
		onLoad() {
			this.list = [{
					iconPath: "/static/newhomeg.png",
					selectedIconPath: "/static/newhomep.png",
					text: '家',
					isDot: true,
					customIcon: false,
					pagePath: '/pages/tabbar1'
				},
				{
					iconPath: "/static/happygrey.png",
					selectedIconPath: "/static/happierp.png",
					text: '聚',
					isDot: true,
					customIcon: false,
					pagePath: '/pages/tabbar2'
				},
				{
					iconPath: "/static/yanblack.png",
					selectedIconPath: "/static/yanpink.png",
					text: '言',
					midButton: true,
					customIcon: false,
					pagePath: '/pages/tabbar3'
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
			treecave() {
				console.log("当前列表:", this.pagelist),
					console.log("当前索引:", this.pagecurrent),
					console.log("跳转到树洞页面"),
					uni.navigateTo({

						url: "/pages/info/treecave"
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
						console.log('返回的数据:', res.data);
						if (res.statusCode === 200) {
							if (res.data.length === 0) {
								this.hasMore = false; // 没有更多数据
							} else {
								if (this.pagelist[this.pagecurrent].type === 'msg') {
									// 处理用户数据
									this.currentItems = [...this.currentItems, ...res.data];
								}
								 //else if (this.pagelist[this.pagecurrent].type === 'posts') {
								// 	// 处理帖子数据
								// 	this.currentItems = [...this.currentItems, ...res.data];
								// }
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
			    if (index >= 0 && index < this.pagelist.length) {
			        this.pagecurrent = index; // 只在有效范围内更新
			        this.swiperCurrent = index;
			        this.page = 1; // 重置页码
			        this.hasMore = true; // 重新设置有更多数据标志
			        this.currentItems = []; // 清空当前用户列表
			        this.loading = true; // 开始加载
			        this.fetchUserList(this.pagelist[index].api, this.page);
			    }
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
			console.log("当前页索引:", this.pagecurrent);
			 console.log("页列表:", this.pagelist);
		}
	};
</script>
<style>
	.container {
		display: flex;
		flex-direction: column;
		height: 100vh;
		/* 占满视口高度 */
		background-color: #fffdfe;
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

	.loading {
		text-align: center;
		padding: 20px;
		font-size: 16px;
		color: #888;
	}
</style>