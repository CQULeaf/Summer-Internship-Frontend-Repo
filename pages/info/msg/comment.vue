<template>
	<view>
		<u-navbar :is-back="true" title="收到的评论" :background="background" :customBack="backtoinfo" height="55">
		</u-navbar>

		<view class="comment" v-for="(res, index) in commentList" :key="res.id">
			<view class="left"><image :src="res.avatar" mode="aspectFill" @click="gotouserpage(res.userId)"></image></view>
			<view class="right">
				<view class="top">
					<view class="name">{{ res.nickname }}</view>
				</view>
				<view class="content">{{ res.comment }}</view>
				</view>
				<view class="bottom">
					{{ res.date }}
				</view>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			
			commentList: [{
				postId:'',
				content:''
			}],
			// 背景颜色
			 background: 
			 {
			 	backgroundColor:'#fed6dc'
			}

		};
	},
	onLoad() {
	},
	
onShow() {
    uni.getStorage({
        key: "nowAccount",
        success: (res) => {
            uni.request({
                url: "http://localhost:8080/ccPost/mypost",
                data: { user_id: res.data.data.userId },
                success: (res2) => {
                    const requests = [];
                    const List = [];

                    for (const key of res2.data.data) {
                        const requestPromise = new Promise((resolve) => {
                            uni.request({
                                url: "http://localhost:8080/comment/getReply",
                                data: { postId: key.postId },
                                success: (fin) => {
                                    for (const key2 of fin.data.data) {
										
										//请求评论用户信息
										uni.request({
											url:"http://localhost:8080/user/getUserInfo",
											data:{userId:key2.userId},
											success(key3) {
												List.push({
												    postId: key.postId,
												    comment: key2.content,
													nickname:key3.data.data.nickname,
													avatar:key3.data.data.avatar,
													date:key3.data.data.createdAt,
													userId:key3.data.data.userId
												});
											}
										})
                                        
                                    }
                                    resolve();
                                },
                                fail: (err) => {
                                    console.error(err);
                                    resolve();
                                }
                            });
                        });
                        requests.push(requestPromise);
                    }

                    Promise.all(requests)
                        .then(() => {
                            console.log(List); 
                            this.commentList = List; 
                        })
                        .catch((err) => {
                            console.error('Error in Promise.all:', err);
                        });
                },
                fail: (resf) => {
                    console.error(resf);
                }
            });
        },
        fail: (res) => {
            console.error(res);
        }
    });
},
	
	methods: {
		backtoinfo()
		{
			uni.switchTab({
				url:"/pages/info/infopage"
			})
			
		},
		
		gotouserpage(index)
		{
			//跳转到用户界面，如果是本人就跳转的个人主页面
			uni.getStorage({
				key:"nowAccount",
				success(account) {
					uni.request({
						url:"http://localhost:8080/user/getUserInfo",
						data:{userId:index},
						success: (res) => {
							console.log(account);
							if(account.data.data.userId!=res.data.data.userId){
								uni.setStorage({
									data:res.data.data,
									key:"userPost",
									success() {
										uni.navigateTo({
											url:"/pages/userPage"
										})
									}
								})
							}else{
								uni.switchTab({
									url:"/pages/me/mypage"
								})
							}
							
						}
					})
				}
			})
			
		}
	}
};
</script>

<style lang="scss" scoped>
.comment {
	display: flex;
	padding: 30rpx;
	background-color: #f5f5f5;
	.left {
		image {
			width: 64rpx;
			height: 64rpx;
			border-radius: 50%;
			background-color: #454545;
		}
	}
	.right {
		flex: 1;
		padding-left: 20rpx;
		font-size: 30rpx;
		.top {
			display: flex;
			justify-content: space-between;
			align-items: center;
			margin-bottom: 10rpx;
			.name {
				color: #5f74fc;
			}
		}
		.content {
			margin-bottom: 10rpx;
		}
		.reply-box {
			background-color: rgb(248, 248, 248);
			border-radius: 12rpx;
			.item {
				padding: 20rpx;
				border-bottom: solid 2rpx $u-border-color;
				.username {
					font-size: 24rpx;
					color: #999999;
				}
			}
		}
		.bottom {
			margin-top: 20rpx;
			display: flex;
			font-size: 24rpx;
			color: #9a9a9a;
			.reply {
				color: #5677fc;
				margin-left: 10rpx;
			}
		}
	}
}
</style>
