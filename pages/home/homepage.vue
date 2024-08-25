<template>
	<view>
		<view class="wrap">
			<view class="u-tabs-box">
				<u-tabs-swiper activeColor="#f29100" ref="tabs" :list="homelist" :current="current" @change="change" :is-scroll="false" swiperWidth="750"></u-tabs-swiper>
			</view>
			<swiper class="swiper-box" :current="swiperCurrent" @transition="transition" @animationfinish="animationfinish">
				<swiper-item class="swiper-item">
					<scroll-view scroll-y style="height: 100%;width: 100%;">
						<u-search placeholder="请输入关键字" v-model="keyword"></u-search>
						<view class="post" v-for="(res, index) in postList" :key="res.id" @search="search" @custom="custom">
							<view class="right">
								<view class="content" @click="toReply(res)">{{ res.title }}</view>
								<view class="u-line-2" @click="toReply(res)">{{ res.postContent }}</view>
								<u-image width="100%" height="300rpx" :src="res.pic"></u-image>
								<view class="like" :class="{ highlight: res.isLike }">
									<view class="num">{{ res.likeCount }}</view>
									<u-icon v-if="!res.isLike" name="thumb-up" :size="30" color="#9a9a9a" @click="getLike(index)"></u-icon>
									<u-icon v-if="res.isLike" name="thumb-up-fill" :size="30" @click="getLike(index)"></u-icon>
								</view>
								<view class="top">
									<view class="nickname">{{ res.nickname }}</view>
									<view class="createdAt">{{ res.createdAt }}</view>
								</view>
							</view>
						</view>
					</scroll-view>
				</swiper-item>
				<swiper-item class="swiper-item">
					<scroll-view scroll-y style="height: 100%;width: 100%;">
						<view class="post" v-for="(res, index) in postList" :key="res.id">
							<view class="right">
								<view class="content" @click="toReply(res)">{{ res.title }}</view>
								<view class="u-line-2" @click="toReply(res)">{{ res.postContent }}</view>
								<u-image width="100%" height="300rpx" :src="res.pic"></u-image>
								<view class="top">
									<view class="nickname">{{ res.nickname }}</view>
									<view class="createdAt">{{ res.createdAt }}</view>
								</view>
							</view>
						</view>
					</scroll-view>
				</swiper-item>
			</swiper>
		</view>
		<u-tabbar v-model="current" :list="list" :mid-button="true"></u-tabbar>
	</view>
</template>

<script>
export default {
	data() {
		return {
			list:'',
			current: 1,
			keyword:'',
			current: 0,
			swiperCurrent: 0,
			dx: 0,
			homelist: [
				{
					name: ' 推荐'
				},
				{
					name: '关注',
				}
			],
			postList:[
				{userId:'',
				postId:'',
				nickname: '',
				createdAt: '',
				postContent: '',
				title:'',
				pic:'',
				likeCount:'',
				isLike:'',}
			]
		};
	},
	
	onShow() {
		
		// uni.request({
		// 	url:'',
		// 	success:(respones) => {
		// 		this.list = respones.data
		// 	}
		// })
		
		this.list = [{
				iconPath: "/static/newhomeg.png",
				selectedIconPath: "/static/newhomep.png",
				text: '家',
				isDot: true,
				customIcon: false,
				pagePath:'/pages/home/homepage'
			},
			{
				iconPath:  "/static/happygrey.png",
				selectedIconPath:"/static/happierp.png",
				text: '聚',
				isDot: true,
				customIcon: false,
				pagePath:'/pages/corner/corner'
			},
			{
				iconPath: "/static/yanblack.png",
				selectedIconPath: "/static/yanpink.png",
				text: '言',
				midButton: true,
				customIcon: false,
				pagePath:'/pages/tabbar3'
			},
			{
				iconPath:  "/static/messagegrey.png",
				selectedIconPath:"/static/messagep.png",
				text: '讯',
				customIcon: false,
				pagePath:'/pages/info/infopage'
			},
			{
				iconPath:  "/static/megrey.png",
				selectedIconPath:"/static/mep.png",
				text: '我',
				isDot: false,
				customIcon: false,
				pagePath:'/pages/me/mypage'
			},
		],
		
		this.postList = [
			{
				id: 1,
				nickname: '叶轻眉',
				createdAt: '12-25 18:58',
				postContent: '我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的',
				title:'不敢相信',
				pic:'/static/c1.png',
				likeCount: 33,
				isLike: false,
			},
			{
				id: 2,
				nickname: '叶轻眉1',
				createdAt: '01-25 13:58',
				postContent: '我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的',
				title:'不敢相信',
				likeCount: 33,
				isLike: false,
			},
			{
				id: 3,
				nickname: '叶轻眉2',
				createdAt: '03-25 13:58',
				postContent: '我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的',
				title:'不敢相信',
				likeCount: 33,
				isLike: false,
			},
			{
				id: 4,
				nickname: '叶轻眉3',
				createdAt: '06-20 13:58',
				postContent: '我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的',
				title:'不敢相信',
				likeCount: 33,
				isLike: false,
			}
		]
	},
	
	methods: {
		// 进入帖子的回复界面
		toReply(index){
			uni.setStorageSync("postData",index)
			console.log(index)
			uni.navigateTo({
				url:'/pages/home/reply',
				
			})
		},
		
		custom(){
			
		},
		
		// 点赞
		getLike(index) {
			this.postList[index].isLike = !this.postList[index].isLike;
			if (this.postList[index].isLike == true) {
				this.postList[index].likeCount++;
			} else {
				this.postList[index].likeCount--;
			}
		},
		
		// tab栏切换
		change(index) {
			this.swiperCurrent = index;
			this.getOrderList(index);
		},
		transition({ detail: { dx } }) {
			this.$refs.tabs.setDx(dx);
		},
		animationfinish({ detail: { current } }) {
			this.$refs.tabs.setFinishCurrent(current);
			this.swiperCurrent = current;
			this.current = current;
		}
	}
};
</script>





<style>
/* #ifndef H5 */
page {
	height: 100%;
	background-color: #f2f2f2;
}
/* #endif */
</style>

<style lang="scss" scoped>
	
.like {
	display: flex;
	align-items: center;
	color: #9a9a9a;
	font-size: 26rpx;
	.num {
		margin-right: 4rpx;
		color: #9a9a9a;
	}
}
	
.wrap {
	display: flex;
	flex-direction: column;
	height: calc(100vh - var(--window-top));
	width: 100%;
}
.swiper-box {
	flex: 1;
}
.swiper-item {
	height: 100%;
}

.post {
	text-align: left;
	display: flex;
	padding: 30rpx;
	.left {
		image {
			width: 64rpx;
			height: 64rpx;
			border-radius: 50%;
			background-color: #f2f2f2;
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
			.nickname {
				color: #c8c9cc;
			}
			.createdAt {
				color: #c8c9cc;
			}
		}
		.content {
			margin-bottom: 10rpx;
			font-size: 20px;
		}
		.text {
			margin-top: 20rpx;
			display: flex;
			font-size: 24rpx;
			color: #9a9a9a;
		}
	}
}
</style>
