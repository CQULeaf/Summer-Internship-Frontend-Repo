<template>
	<view class="container">
		<!-- 切换标签部分 -->
		<view>
			<u-tabs-swiper ref="uTabs" :list="list" :current="current" @change="tabsChange" :is-scroll="false"
						   swiper-Width="100" :bold="false" bg-color="#ffffff"
						   active-color="#ffa6af"></u-tabs-swiper>
		</view>

		<!-- 用户列表部分 -->
		<swiper class="swiper" :current="swiperCurrent" @transition="transition" @animationfinish="animationfinish">
			<swiper-item v-for="(tab, tabindex) in list" :key="tabindex">
				<scroll-view class="scroll-view" scroll-y @scrolltolower="onreachBottom">
					<view class="list">
						<!-- 用户列表 -->
						<view v-if="list[current].type === 'like'">
							<view class="list-item" v-for="(item, index) in currentItems" :key="index">
								<image class="useravatar" :src="item.cover"></image>
								<text class="item-title">{{item.name}}</text>
							
							</view>
						</view>
						
						<!-- 帖子列表 -->
						<view v-if="list[current].type === 'recommend'">
							<!-- 渲染指令 -->
							<view class="list-item" v-for="(post, index) in currentItems" :key="index">
								<!-- key为每个渲染元素 提供唯一的key属性 -->
								<image class="useravatar" :src="post.cover"></image>
								<text class="item-title">{{post.name}}</text>
								
								<!-- post.title元素属性 -->
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
			return {
				list: [
					{
						name: '关注',
						type: 'like',
						api: 'http://127.0.0.1:4523/m1/5010181-4669608-default/corner/superWordNameConcern?user_id',
						//api连接
						//为什么两个url都能用？之后可能要修改
						
					},
					{
						name: '推荐',
						type: 'recommend',
						api: 'http://127.0.0.1:4523/m1/5010181-4669608-default/corner/superWordNameRecommend?user_id'
					}
				],
				current: 0,
				swiperCurrent: 0,
				currentItems: [],
				dataCache: {},
				loading: false,
				page: 1,//分页变量
				hasMore: true,
				touchStartX: 0,
				touchEndX: 0,
				touchThreshold: 30
			};
		},
		methods: {
			onreachBottom() {
				if (this.loading || !this.hasMore) return;
				this.page++;
				this.fetchUserList(this.list[this.current].api, this.page);
			},
			fetchUserList(apiEndpoint, page) {
			    this.loading = true;
			    uni.request({
			        url: `${apiEndpoint}?page=${page}`,
			        method: 'GET',
			        success: (res) => {
						console.log('API响应:', res.data);
						    console.log('API响应类型:', typeof res.data);
			            console.log('请求成功:', res);
						 console.log('返回的数据:', res.data.data);
			            if (res.statusCode === 200) {
							
							
							
							let items = res.data;
							        // 确保items是数组
							        if (items.length === 0) {
							            this.hasMore = false;
							        } else {
							            this.currentItems = [...this.currentItems, ...items.data];
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
			            console.error('请求失败:', err);
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
				this.page = 1;//充值页码
				this.hasMore = true;//重新设置更多数据标志
				this.currentItems = [];//清空用户列表
				this.loading = true;//开始价值
				this.fetchUserList(this.list[index].api, this.page);
			},
			transition(e) {
				this.handleTouchEnd(e);//处理滑动
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
									this.loading = true;
									this.fetchUserList(this.list[current].api, this.page);
								} else {
									this.currentItems = this.dataCache[this.list[current].api];
									this.loading = false;
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
					.slot-wrap{
						padding: 0 50rpx;
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