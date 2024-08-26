<template>
	<view>
		<u-navbar :is-back="true" title="收到的点赞" :background="background" :customBack="backtoinfo" height="55">
		</u-navbar>

		<view class="comment" v-for="(res, index) in commentList" :key="res.id">
			<view class="left"><image :src="res.url" mode="aspectFill" @click="gotouserpage(res.id)"></image></view>
			<view class="right">
				<view class="top">
					<view class="name" @click="gotouserpage(res.id)">{{ res.name }}</view>
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
			console.log("返回到 infopage");
			uni.switchTab//如果你的目标页面是 TabBar 页面，请使用 uni.switchTab 方法来进行跳转。
			             //TabBar 页面是指在移动应用中，通常位于底部的导航栏，用于快速切换不同的页面或功能模块。它允许用户在应用程序的多个主要视图之间进行切换，通常显示为一组图标和文本。
			({
				url:"/pages/info/infopage",
				   success: () => {
				        console.log("成功跳转到 infopage");
				    },
				    fail: (err) => {
				        console.error("跳转失败:", err);
				    }
			})
		},
		
		// 评论列表
		getComment() {
			this.commentList = [
				{
					id: 1,
					name: '叶旭航',
					date: '12-25 18:58',
					contentText: '赞了你的评论',
					url: 'https://cdn.uviewui.com/uview/template/SmilingDog.jpg',
					replyList: [
						{
							name: '雷焱丹',
							contentStr: '我们组长特别聪明，跟我一样'
						}
					]
				},
				{
					id: 2,
					name: '陈榕',
					date: '01-25 13:58',
					contentText: '赞了你的帖子',
					url: 'https://cdn.uviewui.com/uview/template/niannian.jpg',
				},
				{
					id: 3,
					name: '马逸民',
					date: '03-25 13:58',
					contentText: '赞了你的评论',
					allReply: 2,
					url: '../../../static/logo.png',
					replyList: [
						
						{
							name: '雷焱丹',
							contentStr: '他长得都没我帅'
						}
					]
				},
				{
					id: 4,
					name: '黄靖杰',
					date: '06-20 13:58',
					contentText: '赞了你的帖子',
					url: 'https://cdn.uviewui.com/uview/template/SmilingDog.jpg',
				},
				{
					id: 5,
					name: '解吴雪',
					date: '06-25 20:40',
					contentText: '赞了你的帖子',
					url: 'https://cdn.uviewui.com/uview/template/niannian.jpg',
				}
			];
		},
		gotouserpage(index){
			uni.request({
				url:"http://localhost:8080/user/getUserInfo",
				data:index,
				method:"GET",
				success: (res) => {
					uni.setStorage({
						data:res,
						key:"userPost"
					})
				}
			})
			uni.navigateTo({
				url:"/pages/userPage"
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
