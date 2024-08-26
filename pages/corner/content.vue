<!-- 点击里面的帖子跳转到post -->
<!-- 超话里面具体内容要看数据库 -->
<template>
				<view class="wrap">
						<!-- 新增的板块，用于展示头像、标题、描述、关注数和帖子数 -->
						<view class="topic-section">
							<image class="cover" :src="topic.cover"></image>
							<view class="topic-info">
								<text class="name">{{ topic.name }}</text>
								<text class="description">{{ topic.description }}</text>
								<view class="stats">
									<text class="stat">关注数：{{ topic.follower_count  }}</text>
									<text class="stat">帖子数：{{ topic.post_count }}</text>
								</view>
							</view>
						</view>
			<!-- 新增的板块，用于展示头像、标题、描述、关注数和帖子数 -->
			
		<u-waterfall v-model="flowList" ref="uWaterfall">
			<!-- 左边里面是帖子而不是topic -->
					<template v-slot:left="{leftList}">
						<view class="demo-warter" v-for="(item, index) in leftList" :key="index" @click="goToPost(item.post_id)">
							<u-lazy-load threshold="-450" border-radius="10" :image="item.image" :index="index"></u-lazy-load>
							<view class="demo-title">
								{{item.title}}
							</view>
							<view class="demo_user_id">
								<!--发布者 -->
								{{item.user_id}}
							</view>
							<!-- 点赞评论 -->
							<view class="post-actions">
							  <view class="comment-btn">
							    <uni-icons type="chat" size="5" color="#2196f3"></uni-icons>
							    <text class="action-text">评论</text>
							  </view>
							</view>
							<u-icon name="close-circle-fill" color="#ffffff" size="34" class="u-close" @click="remove(item.post_id)"></u-icon>
						</view>
					</template>
					
					<!-- 右边 --><!-- 先不改形成对照，不太确定怎么改呢 -->
					<template v-slot:right="{rightList}">
						<view class="demo-warter" v-for="(item, index) in rightList" :key="index" @click="goToPost(item.post_id)">
							<u-lazy-load threshold="-450" border-radius="10" :image="item.image" :index="index"></u-lazy-load>
							<view class="demo-title">
								{{item.title}}
							</view>
							<view class="demo_user_id">
								{{item.user_id}}
							</view>
							<view class="post-actions">
							  <view class="comment-btn">
							    <uni-icons type="chat" size="5" color="#2196f3"></uni-icons>
							    <text class="action-text">评论</text>
							  </view>
							</view>
							<u-icon name="close-circle-fill" color="#ffffff" size="34" class="u-close" @click="remove(item.id)"></u-icon>
						</view>
					</template>
				</u-waterfall>
				<u-loadmore bg-color="rgb(240, 240, 240)" :status="loadStatus" @loadmore="addRandomData"></u-loadmore>
			</view>
	</view>
</template>



<script>
	export default {
		data() {
			return {
				
				//---------------超话的变量
				topic: {
							cover: "/static/demodemo.jpg",
							name: '元白超话   ',
							description: '君埋泉下泥销骨,我寄人间雪满头',
							follower_count : 8080,
							post_count: 5678,
							post_id:'',
						},
				//---------------超话的变量
				loadStatus: 'loadmore',//加载状态
				flowList: [],//瀑布流数据
				
				//--------------------------------------------返回的帖子数据
				list: [//需要api进行连接，生成不同的图片和标题，昵称，头像
					{
						post_id:'',
						title: '我今因病魂颠倒,唯梦闲人不梦君',
						user_id: '元稹',
						image: "/static/demodemo.jpg",
					},
					{
						post_id:'',
						title: '不知忆我因何事,昨夜三回梦见君',
						user_id: '白居易',
						image: 'http://pic.sc.chinaz.com/Files/pic/pic9/202002/zzpic23325_s.jpg',
					},
					
					
				],
				//--------------------------------------------返回的帖子数据
				 isLiked: false, 
			}
		},
		onLoad() {
			this.addRandomData();
			uni.getStorage({
				key:'matchuser2',
				success(res) {
					uni.request({
						url:'http://localhost:8080/ccPost/getPostsByTopicId',
						data:res,
						success(suc) {
							this.list=suc
						}
					})
				}
			})
		},
		onReachBottom() {
			this.loadStatus = 'loading';
			// 模拟数据加载
			setTimeout(() => {
				this.addRandomData();
				this.loadStatus = 'loadmore';
			}, 1000)
		},
		methods: {

			addRandomData() {
				for(let i = 0; i < 10; i++) {
					let index = this.$u.random(0, this.list.length - 1);
					// 先转成字符串再转成对象，避免数组对象引用导致数据混乱
					let item = JSON.parse(JSON.stringify(this.list[index]))
					item.id = this.$u.guid();
					this.flowList.push(item);
				}
			},//增加随机数据
			
			remove(id) {
				this.$refs.uWaterfall.remove(id);
			},//删除

			//-----------------------------点击帖子，跳转到帖子详情页面+获取想要的信息
			        goToPost(post_id) {
			         
						uni.request({
							url: "http://localhost:8080/ccPost/getPost",//api
							data: post_id,//自己定义的 变量，包含api中需要传递的信息
							method: 'GET',//方法类型
							success: (res) => {
								console.log(res);
								if (res.statusCode == 200) {
									const value = res.data.data; // 假设 API 返回的数据格式包含用户信息
									//获取想要的信息
									
									console.log(res.data);//打印
									uni.setStorage({//缓存
										key: 'postData',//就是之前定义的变量
										data: this.value,
										success: function() {
											console.log('噫，好了，我中了');
											uni.navigateTo({
												url:'/pages/home/reply',
											})
											
										}
									});
								} else {
									uni.showToast({
										title: '获取数据失败',
										icon: 'none'
									});
								}
							}
						})
			        },
			
		}
	}
</script>

//------------------------------------
<style>
    /* page不能写带scope的style标签中，否则无效 */
    page {
        background-color: rgb(240, 240, 240);
        /* 添加页面的上下内边距 */
        padding-top: 10px; /* 页面顶部内边距 */
        padding-bottom: 100px; /* 页面底部内边距 */
    }
</style>
//------------------------------------

<style lang="scss" scoped>
	//--------------------瀑布流
	.demo-warter {
		border-radius: 8px;
		margin: 5px;
		background-color: #ffffff;
		padding: 8px;
		position: relative;
		/* 增加外边距以确保与上下内容保持距离 */
	
		
	}//排版
	
	.u-close {
		position: absolute;
		top: 32rpx;
		right: 32rpx;
	}//叉号
	
	.demo-image {
		width: 100%;
		border-radius: 4px;
	}
	
	.demo-title {
		font-size: 30rpx;
		margin-top: 5px;
		color: $u-main-color;
	}
	
	.demo-user_id {
		font-size: 22rpx;
		color: $u-tips-color;
		margin-top: 5px; 
	}//名字
	
//-------------------上方框框的位置
.wrap {
		padding-top: 0rpx; // 减少顶部内边距，使内容更靠近顶部
	
	}
	.topic-section {
			display: flex;
			align-items: center;
			background-color: #ffffff;
			padding:50rpx;//超话框的宽度
			margin-bottom: 10rpx; //超话和帖子之间的距离
			
		
		}
		
		.cover {
			width: 100rpx;
			height: 100rpx;
			border-radius: 50%;
			margin-right: 20rpx;
		}
		
		.topic-info {
			flex: 1;
		}
		
		.title {
			font-size: 30rpx;
			font-weight: bold;
		}
		
		.description {
			font-size: 28rpx;
			color: #666;
			margin-top: 0rpx;
		}
		
		.stats {
			display: flex;//没啥用
			margin-top: 20rpx;//关注和介绍的距离
		}
		
		.stat {
			font-size: 24rpx;//字体大小
			color: #999;
			margin-right: 30rpx;//关注和帖子的排版
		}
		
		//--------------------------
		.post-actions {
		  display: flex;
		  justify-content: space-between;
		  align-items: center;
		  margin-top: 10px;
		}
		
		.comment-btn {
		  background-color: #ffffff;
		  border: none;
		  padding: 1px;
		  font-size:1px;
		}
	   //-------------
</style>