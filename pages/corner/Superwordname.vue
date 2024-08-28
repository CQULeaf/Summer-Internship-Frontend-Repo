<template>
	<view>
		<view>
			<view class="list-item" v-for="(item, index) in list" :key="index" >
				<text class="item-title" @click="goToContent(item.topicId)">{{item.name}}</text>
			</view>
			<u-divider>还没有关注任何话题</u-divider>
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
					  }],
				current: 4,
				pagecurrent: 0,
				swiperCurrent: 0,
				
				currentItems:[{	topicId:'',
						name: '',
						cover:'',
						description:'',
						postCount:'',
						followerCount:'',
						flag: '',
					  },],
				
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
				},
				
				Update:{
					userId:'',
					flag:''
				}
			};
		},
		onLoad() {
			
		},
		onShow() {
			uni.getStorage({
				key: 'nowAccount',  
				success: (res) => {  
					this.currentUserId = res.data.data.userId; // 获取当前用户 ID  
					//console.log('获取到的当前userId:', this.currentUserId);  
				},
			});
			this.getTopic();
			//console.log(this.list)
		},

		methods: {
			getTopic() {
				uni.getStorage({
					key: 'matchuser1',
					success: (res) => {
						this.list = res.data;
					},
				});
			},
			goToContent(topicId) {
				console.log(topicId)
				uni.request({
					url: "http://localhost:8080/corner/superWordNameConcern",
					data: { topicId: topicId },
					method: 'GET',
					success: (res) => {
						//console.log(res)
						if (res.statusCode == 200) {
							this.matchuser2 = res.data.data;
							uni.setStorage({
								key: 'matchuser2',
								data: this.matchuser2,
								success: () => {
									console.log(this.matchuser2);
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
		},
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
