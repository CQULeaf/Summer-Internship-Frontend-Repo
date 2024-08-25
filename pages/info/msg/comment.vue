<template>
	<view>
		<u-navbar :is-back="true" title="收到的评论" :background="background" :customBack="backtoinfo" height="55">
		</u-navbar>

		<view class="comment" v-for="(res, index) in commentList" :key="res.id">
			<view class="left"><image :src="res.url" mode="aspectFill"></image></view>
			<view class="right">
				<view class="top">
					<view class="name">{{ res.name }}</view>
				</view>
				<view class="content">{{ res.contentText }}</view>
				<view class="reply-box">
					<view class="item" v-for="(item, index) in res.replyList" :key="item.index">
						<view class="username">{{ item.name }}</view>
						<view class="text">{{ item.contentStr }}</view>
					</view>
					
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
			commentList: [],
			// 背景颜色
			 background: 
			 {

				// 背景颜色
			 	backgroundColor:'#fed6dc'
			}

		};
	},
	onLoad() {
		this.getComment();
	},
	methods: {
		backtoinfo()
		{
			uni.switchTab({
				url:"/pages/info/infopage"
			})
			
		},
		
		// 评论列表
		getComment() {
			this.commentList = [
				{
					id: 1,
					name: '韩刚',
					date: '08-29 13:58',
					contentText: '评论了你：真棒！',
					url: '../../../static/logo.png',
				},
				{
					id: 2,
					name: '叶旭航',
					date: '12-25 18:58',
					contentText: '回复：哈哈哈，你不仅聪明，长得也帅',
					url: 'https://cdn.uviewui.com/uview/template/SmilingDog.jpg',
					replyList: [
						{
							name: '雷焱丹',
							contentStr: '我们组长特别聪明，跟我一样'
						}
					]
				},
				{
					id: 3,
					name: '陈榕',
					date: '01-25 13:58',
					contentText: '评论了你：丹丹好棒！',
					url: 'https://cdn.uviewui.com/uview/template/niannian.jpg',
				},
				{
					id: 4,
					name: '马逸民',
					date: '03-25 13:58',
					contentText: '回复：我也觉得他没你帅',
					url: '../../../static/logo.png',
					replyList: [
						
						{
							name: '雷焱丹',
							contentStr: '他长得都没我帅'
						}
					]
				},
				{
					id: 5,
					name: '黄靖杰',
					date: '06-20 13:58',
					contentText: '评论了你：好强啊',
					url: 'https://cdn.uviewui.com/uview/template/SmilingDog.jpg',
				},
				{
					id: 6,
					name: '解吴雪',
					date: '06-25 20:40',
					contentText: '评论了你：棒棒！',
					url: 'https://cdn.uviewui.com/uview/template/niannian.jpg',
				}
			];
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
