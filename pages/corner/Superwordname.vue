<template>
	<view>
		<view>
			<view class="slot-wrap">
				<u-tabs-swiper ref="uTabs" :list="pagelist" :current="pagecurrent" @change="tabsChange"
					:is-scroll="false" :bold="false" bg-color="#ffc7cb" height=120 active-color="#000000"></u-tabs-swiper>
			</view>
			<swiper class="swiper" :current="swiperCurrent">
				<swiper-item v-for="(tab, tabindex) in pagelist" :key="tabindex">
					<scroll-view class="scroll-view" scroll-y>
						<view class="list">
							
							<view v-if="pagelist[pagecurrent].type === 'recommend'">
								<view class="list-item" v-for="(item, index) in list" :key="index" @click="goToContent(item.topic_id)">
									<!-- 这里可以显示item的相关属性 -->
									<text class="item-title">{{item.name}}</text>
									<view>{{ item.cover }}</view>
								</view>
							</view>
							
							<view v-if="pagelist[pagecurrent].type === 'like'">
								<view class="list-item" v-for="(item, index) in currentItems" :key="index" @click="goToContent(item.topic_id)">
									<!-- 这里可以显示item的相关属性 -->
									<text class="item-title">{{item.name}}</text>
									<view>{{ item.cover }}</view>
									
								</view>
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
				list:[{	topicId:'',
						name: '',
						cover:'',
						description:'',
						postCount:'',
						followerCount:'',
						flag: '',
					  },],
				current: 4,
				pagelist: [
					{name: '关注',
						type: 'like',
						url: "http://localhost:8080/corner/getTopicsByFlagAndUser",
					},
					{
						name: '推荐',
						type: 'recommend',
						url: "http://localhost:8080/corner/getTopicsByFlagAndUser",
					}
				],
				pagecurrent: 0,
				swiperCurrent: 0,
				
				currentItems: [{name: '', cover: '', description: '', post_count: '', follower_count: ''},
				{name: '', cover: '', description: '', post_count: '', follower_count: ''}],
				dataCache: {},
				loading: false,
				page: 1,
				hasMore: true,
				touchStartX: 0,
				touchEndX: 0,
				touchThreshold: 30,
				postList: [{
					title: ''
				}],
				currentUserId:'',
				
				dataofgetTopicsByFlagAndUser:{
					flag:'',
					userId:''
				}
			};
		},
		onLoad() {
			
		},
		onShow() {
			this.getTopic();
			 this.getLike();
			 this.getRecommendations();
			 console.log(this.list)
		},

		methods: {
			getTopic() {
				uni.getStorage({
					key: 'matchuser1',
					success: (res) => {
						console.log(res)
						this.list = res.data;
					},
				});
			},
			
			getLike() {
			    uni.request({
			        url: "http://localhost:8080/corner/getTopicsByFlag",
			        data: { flag: '专业'  }, // 获取推荐的内容
			        method: 'GET',
			        success: (res) => {
			            if (res.statusCode == 200) {
							console.log('噫，好了，我中了');
			                this.currentItems = res.data.data; // 用实际数据格式替换
			            } else {
			                uni.showToast({
			                    title: '获取推荐内容失败',
			                    icon: 'none'
			                });
			            }
			        },
			        fail: () => {
			            console.log('请求失败');
			        }
			    });
			},
			
			        getRecommendations() {
			            uni.request({
			                url: "http://localhost:8080/corner/getTopicsByFlag",
			                data: { flag: '社团'  }, // 获取推荐的内容
			                method: 'GET',
			                success: (res) => {
			                    if (res.statusCode == 200) {
									console.log('噫，好了，我中了');
			                        this.currentItems = res.data.data; // 用实际数据格式替换
			                    } else {
			                        uni.showToast({
			                            title: '获取推荐内容失败',
			                            icon: 'none'
			                        });
			                    }
			                },
			                fail: () => {
			                    console.log('请求失败');
			                }
			            });
			        },
			goToContent(topicId) {
				uni.request({
					url: "http://localhost:8080/corner/superWordNameConcern",
					data: { topic_id: topicId },
					method: 'GET',
					success: (res) => {
						if (res.statusCode == 200) {
							this.matchuser2 = res.data.data;
							uni.setStorage({
								key: 'matchuser2',
								data: this.matchuser2,
								success: () => {
									uni.navigateTo({
										url: '/pages/corner/content',
									});
								}
							});
						} else {
							uni.showToast({
								title: '获取数据失败',
								icon: 'none'
							});
						}
					},
					fail: () => {
						console.log('请求失败');
					}
				});
			},

			tabsChange(index) {
			            this.swiperCurrent = index;
			            this.pagecurrent = index;
			            this.page = 1; 
			            this.hasMore = true;
			            this.currentItems = []; 
			            this.loading = true; 
			
			            if (this.pagelist[index].type === 'like') {
			                this.getLike(); 
			            } else if (this.pagelist[index].type === 'recommend') {
			                this.getRecommendations(); // 如果是推荐标签，调用推荐数据的方法
			            }
			        },
		},
		mounted() {  
					uni.getStorage({  
						key: 'nowAccount',  
						success: (res) => {  
							this.currentUserId = res.data.data.userId; // 获取当前用户 ID  
							//console.log('获取到的当前userId:', this.currentUserId);  
						},  
					});  
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
