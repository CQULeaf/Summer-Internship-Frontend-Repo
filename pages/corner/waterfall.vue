<template>
	<view class="wrap">
		<!-- 新增的板块，用于展示头像、标题、描述、关注数和帖子数 -->
				<view class="profile-section">
					<image class="avatar" src="http://example.com/avatar.jpg"></image>
					<view class="profile-info">
						<text class="title">用户昵称</text>
						<text class="description">这里是帖子描述，可以写一些用户介绍或者帖子内容概要。</text>
						<view class="stats">
							<text class="stat">关注数：1234</text>
							<text class="stat">帖子数：5678</text>
						</view>
					</view>
				</view>
			
			
		<u-waterfall v-model="flowList" ref="uWaterfall">
			
			<template v-slot:left="{leftList}">
				<view class="demo-warter" v-for="(item, index) in leftList" :key="index">
					<u-lazy-load threshold="-450" border-radius="10" :image="item.image" :index="index"></u-lazy-load>
					<view class="demo-title">
						{{item.title}}
					</view>
					
					<view class="demo-tag">
						<view class="demo-tag-owner">
							新乐府
						</view>
						<view class="demo-tag-text">
							元白
						</view>
					</view>
					<view class="demo-shop">
						{{item.shop}}
					</view>
					<u-icon name="close-circle-fill" color="#ffffff" size="34" class="u-close" @click="remove(item.id)"></u-icon>
				</view>
			</template>
			
			
			
			<template v-slot:right="{rightList}">
				<view class="demo-warter" v-for="(item, index) in rightList" :key="index">
					<u-lazy-load threshold="-450" border-radius="10" :image="item.image" :index="index"></u-lazy-load>
					<view class="demo-title">
						{{item.title}}
					</view>
					
					<view class="demo-tag">
						<view class="demo-tag-owner">
							新乐府 
						</view>
						<view class="demo-tag-text">
							元白
						</view>
					</view>
					<view class="demo-shop">
						{{item.shop}}
					</view>
					<u-icon name="close-circle-fill" color="#ffffff" size="34" class="u-close" @click="remove(item.id)"></u-icon>
				</view>
			</template>
		</u-waterfall>
		<u-loadmore bg-color="rgb(240, 240, 240)" :status="loadStatus" @loadmore="addRandomData"></u-loadmore>
	</view>
</template>



<script>
	export default {
		data() {
			return {
				
				//---------------帖子
				profile: {
							avatar: 'http://example.com/avatar.jpg',
							title: '用户昵称',
							description: '这里是帖子描述...',
							followers: 1234,
							posts: 5678
						},
				//---------------帖子		
				
				loadStatus: 'loadmore',//加载状态
				flowList: [],//瀑布流数据
				list: [
					{//需要api进行连接，生成不同的图片和标题，昵称，头像
						
						title: '我今因病魂颠倒,唯梦闲人不梦君',
						shop: '元稹',
						image: 'http://pic.sc.chinaz.com/Files/pic/pic9/202002/zzpic23327_s.jpg',
					},
					{
						
						title: '不知忆我因何事,昨夜三回梦见君',
						shop: '白居易',
						image: 'http://pic.sc.chinaz.com/Files/pic/pic9/202002/zzpic23325_s.jpg',
					},
					
					
				]
			}
		},
		onLoad() {
			this.addRandomData();
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
			
		}
	}
</script>

//------------------------------------
<style>
    /* page不能写带scope的style标签中，否则无效 */
    page {
        background-color: rgb(240, 240, 240);
        /* 添加页面的上下内边距 */
        padding-top: 100px; /* 页面顶部内边距 */
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
	
	.demo-tag {
		display: flex;
		margin-top: 5px;
	}//两个小框框的排版
	
	.demo-tag-owner {//红色小框框
		background-color: $u-type-error;
		color: #FFFFFF;
		display: flex;
		align-items: center;
		padding: 4rpx 14rpx;
		border-radius: 50rpx;
		font-size: 20rpx;
		line-height: 1;
	}
	
	.demo-tag-text {
		border: 1px solid $u-type-primary;
		color: $u-type-primary;
		margin-left: 10px;
		border-radius: 50rpx;
		line-height: 1;
		padding: 4rpx 14rpx;
		display: flex;
		align-items: center;
		border-radius: 50rpx;
		font-size: 20rpx;
	}//蓝色小框框
	
	
	.demo-shop {
		font-size: 22rpx;
		color: $u-tips-color;
		margin-top: 5px; 
	}//名字
	
//-------------------上方框框的位置
.wrap {
		padding-top: 0rpx; // 减少顶部内边距，使内容更靠近顶部
	
	}
	.profile-section {
			display: flex;
			align-items: center;
			background-color: #ffffff;
			padding:50rpx;//超话框的宽度
			margin-bottom: 10rpx; //超话和帖子之间的距离
		
		}
		
		.avatar {
			width: 100rpx;
			height: 100rpx;
			border-radius: 50%;
			margin-right: 20rpx;
		}
		
		.profile-info {
			flex: 1;
		}
		
		.title {
			font-size: 34rpx;
			font-weight: bold;
		}
		
		.description {
			font-size: 28rpx;
			color: #666;
			margin-top: 10rpx;
		}
		
		.stats {
			display: flex;
			margin-top: 10rpx;
		}
		
		.stat {
			font-size: 24rpx;
			color: #999;
			margin-right: 20rpx;
		}
</style>