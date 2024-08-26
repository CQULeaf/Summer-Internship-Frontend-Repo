<template>
	<view>
		<view v-model="this.user">
			<view class="u-demo-wrap">
				<view class="u-demo-area">
					<u-toast ref="uToast"></u-toast>
					<view class="u-avatar-wrap">
						<image class="u-avatar-demo" v-if="this.user.avatar" :src="this.user.avatar"></image>
						<view class="u-p-b-20">{{this.user.nickname}}</view>
						<u-button  shape="circle" size="medium" @click="follow">{{this.followText}}</u-button>
					</view>
				</view>
			</view>

			<!-- <u-grid :col="8" :border="false" >
				<u-grid-item>
					<view class="grid-text">朋友</view>
				</u-grid-item>
				<u-grid-item>
					<view class="grid-text">关注</view>
				</u-grid-item>
				<u-grid-item>
					<view class="grid-text">粉丝</view>
				</u-grid-item>
				<u-grid-item></u-grid-item>
				<u-grid-item></u-grid-item>
				<u-grid-item></u-grid-item>
				<u-grid-item></u-grid-item>
				<u-grid-item></u-grid-item>
			</u-grid> -->
			<view class="slot-wrap"><!-- 通过自定义slot传入的内容 -->
				<u-tabs-swiper ref="uTabs" :list="pagelist" :current="current" @change="tabsChange" :is-scroll="false"
					 :bold="false" bg-color="#ffc7cb"  active-color="#000000"></u-tabs-swiper>
			</view>
			<swiper class="swiper" :current="swiperCurrent" @transition="transition" @animationfinish="animationfinish">
				<swiper-item v-for="(tab, tabindex) in pagelist" :key="tabindex">
					<scroll-view class="scroll-view" scroll-y @scrolltolower="onreachBottom">
						<view class="list">
							<!-- 用户列表 -->
							<view class="list-item" v-for="(item, index) in currentItems" :key="index">
								<image class="useravatar" :src="item.avatar"></image>
								<text class="item-title">{{item.username}}</text>
							</view>
						</view>
					</scroll-view>
				</swiper-item>
			</swiper>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				user: {
					avatar: '',
					nickname: '1111',
					username:''
				},
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
							api: 'http://127.0.0.1:4523/m1/5010181-4669608-default/user/friends'
						},
						{
							name: '点赞',
							type: 'liked',
							api: 'http://127.0.0.1:4523/m1/5010181-4669608-default/user/follwing'
						},
					],
					followSta:false,
					followText:'关注',
					current: 0, //变量名：变量值
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

		onShow() {
			//获取用户信息
			uni.getStorage({
				key:"userPost",
				success: (res) =>{
					console.log(res);
					this.user=res.data
					console.log(this.user);
				}
			})
			//获取用户关注信息
			console.log(this.user);
		},
		
		methods: {
			follow(){
				this.followSta=!this.followSta;
				if(this.followSta==false){
					this.followText="关注"
				}else{
					this.followText="已关注"
				}
			},
			onreachBottom() {
					if (this.loading || !this.hasMore) return; // 正在加载中或没有更多数据则不执行
					this.page++;
					this.fetchUserList(this.pagelist[this.current].api, this.page);
				},
				//发送请求
				fetchUserList(apiEndpoint, page) {
					this.loading = true;
					uni.request({
						url: `${apiEndpoint}?page=${page}`, //在fetchUserList方法中的请求URL使用了'${apiEndpoint}?page=${page}'，这会导致字符串直接拼接而非动态生成变量。正确的做法应该是使用模板字符串的语法来动态替换变量
						method: 'GET',
						success: (res) => {
							// console.log('请求成功:', res);
							// console.log('返回的数据:', res.data.data);
							if (res.statusCode === 200) {
								if (res.data.length === 0) {
									this.hasMore = false; // 没有更多数据
								} else {
									// 处理用户数据
									this.currentItems = [...this.currentItems, ...res.data.data];
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
					this.fetchUserList(this.pagelist[index].api, this.page);
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
					} else if (current >= this.pagelist.length) {
						current = this.pagelist.length - 1;
					}
					this.$refs.uTabs.setFinishCurrent(current);
					this.swiperCurrent = current;
					this.current = current;
			
					if (!this.dataCache[this.pagelist[current].api]) {
						this.loading = true; // 开始加载
						this.fetchUserList(this.pagelist[current].api, this.page);
					} else {
						this.currentUsers = this.dataCache[this.pagelist[current].api];
						this.loading = false; // 数据已缓存，无需再次加载
					}
				}
			
			},
			mounted() {
				this.fetchUserList(this.pagelist[this.current].api, this.page);
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
	.grid{
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
</style>