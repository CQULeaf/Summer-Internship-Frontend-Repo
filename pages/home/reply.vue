<template>
	<view class="wrap">
		<view class="comment">
			<view class="top">
				<view class="left">
					<view class="heart-photo"><image :src="comment.pic" mode=""></image></view>
					<view class="user-info">
						<view class="name">{{ comment.nickname }}</view>
						<view class="date">{{comment.createdAt}}</view>
					</view>
				</view>
				<view class="right" :class="{ highlight: comment.isLike }">
					{{ comment.likeCount }}
					<u-icon v-if="!comment.isLike" name="thumb-up" class="like" color="#9a9a9a" :size="30" @click="getLike"></u-icon>
					<u-icon v-if="comment.isLike" name="thumb-up-fill" class="like" :size="30" @click="getLike"></u-icon>
				</view>
			</view>
			<view class="content u-font-40">{{ comment.title}}</view>
			<view class="content u-p-t-30" >{{ comment.postContent }}</view>
			<image v-if="comment.pic" :src="comment.pic" mode=""></image>
		</view>
		
		<!-- 回复 -->
		<view class="all-reply">
			<view class="all-reply-top">全部回复{{ comment.allReply }}</view>
			<view class="item" v-for="(item, index) in commentList" :key="index">
				<view class="comment">
					<view class="top">
						<view class="left">
							<view class="heart-photo"><image :src="item.url" mode=""></image></view>
							<view class="user-info">
								<view class="name">{{ item.nickname }}</view>
								<view class="date">{{ item.createdAt }}</view>
							</view>
						</view>
						<view class="right"  :class="{ highlight: item.isLike }">
							<view class="num">{{ item.likeCount }}</view>
							<u-icon v-if="!item.isLike" name="thumb-up" class="like" :size="30" color="#9a9a9a" @click="getLike(index)"></u-icon>
							<u-icon v-if="item.isLike" name="thumb-up-fill" class="like" :size="30" @click="getLike(index)"></u-icon>
						</view>
					</view>
					<view class="content">{{ item.contentText }}</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			comment:{
				userId:'',
				nickname: '',
				date: '',
				postContent: '',
				title:'',
				pic:'',
				likeCount:'',
				isLike:'',
				},
			commentList: [],
		};
	},
	onLoad() {
		this.getReply();
	},
	
	onShow(){
		this.comment=uni.getStorageSync('postData');
	},
	
	methods: {
		// 点赞
		getLike(index) {
			if (index === 0 || index > 0) {
				this.commentList[index].isLike = !this.commentList[index].isLike;
				if (this.commentList[index].isLike == true) {
					this.commentList[index].likeCount++;
				} else {
					this.commentList[index].likeCount--;
				}
			} else {
				if (this.comment.isLike == true) {
					this.comment.isLike = !this.comment.isLike;
					this.comment.likeCount--;
				} else {
					this.comment.isLike = !this.comment.isLike;
					this.comment.likeCount++;
				}
			}
		},

		// 回复列表
		getReply() {
			
			this.commentList = [
				{
					name: '新八几',
					date: '12-25 18:58',
					contentText: '不要乱打广告啊喂！虽然是真的超好用',
					url: 'https://cdn.uviewui.com/uview/template/SmilingDog.jpg',
					likeCount: 33,
					isLike: false,
				},
				{
					name: '叶轻眉1',
					date: '01-25 13:58',
					url: 'https://cdn.uviewui.com/uview/template/SmilingDog.jpg',
					contentText: '我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的',
					allReply: 0,
					likeCount: 11,
					isLike: false,
				},
				{
					name: '叶轻眉2',
					date: '03-25 13:58',
					contentText: '我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的',
					allReply: 0,
					likeCount: 21,
					url: 'https://cdn.uviewui.com/uview/template/SmilingDog.jpg',
					isLike: false,
					allReply: 2,
				},
				{
					name: '叶轻眉3',
					date: '06-20 13:58',
					contentText: '我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的',
					allReply: 0,
					likeCount: 150,
					url: 'https://cdn.uviewui.com/uview/template/SmilingDog.jpg',
					isLike: false
				}
			];
		}
	}
};
</script>

<style lang="scss" scoped>
page {
	background-color: #f2f2f2;
}
.comment {
	padding: 30rpx;
	font-size: 32rpx;
	background-color: #ffffff;
	.top {
		display: flex;
		justify-content: space-between;
	}
	.left {
		display: flex;
		.heart-photo {
			image {
				width: 64rpx;
				height: 64rpx;
				border-radius: 50%;
				background-color: #f2f2f2;
			}
		}
		.user-info {
			margin-left: 10rpx;
			.name {
				color: #5677fc;
				font-size: 28rpx;
				margin-bottom: 4rpx;
			}
			.date {
				font-size: 20rpx;
				color: $u-light-color;
			}
		}
	}
	.right {
		display: flex;
		font-size: 20rpx;
		align-items: center;
		color: #9a9a9a;
		.like {
			margin-left: 6rpx;
		}
		.num{
			font-size: 26rpx;
			color: #9a9a9a;
		}
	}
	.highlight {
		color: #5677fc;
		.num{
			color: #5677fc;
		}
	}
}
.all-reply {
	margin-top: 10rpx;
	padding-top: 20rpx;
	background-color: #ffffff;
	.all-reply-top {
		margin-left: 20rpx;
		padding-left: 20rpx;
		border-left: solid 4rpx #5677fc;
		font-size: 30rpx;
		font-weight: bold;
	}
	.item {
		border-bottom: solid 2rpx $u-border-color;
	}
}
</style>
