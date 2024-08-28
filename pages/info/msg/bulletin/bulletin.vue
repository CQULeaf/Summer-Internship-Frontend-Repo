<template>
	<view>
		<u-navbar :is-back="true" title="公告" :background="background" :customBack="backtoinfo" height="55" title-size=40>
		</u-navbar>
		<view class="bulletin" v-for="(res, index) in bulletinList" :key="res.id">
			<view class="right">
				<view class="content">{{ res.bulletinTitle }}</view>
				<view class="top">
					<view class="date">{{ res.date }}</view>
				</view>
				<view class="u-line-2">{{ res.bulletinText }}</view>
				<u-cell-item class="u-p-l-2" title="详情" @tap="toAllReply(index)"></u-cell-item>
				<u-line color="#dedede"/>
			</view>
		</view>
		<u-divider>没有更多了</u-divider>
	</view>
</template>

<script>
export default {
	data() {
		return {
			bulletinList: '',
			background:
			 {
				// 背景颜色
			 	backgroundColor:'#fed6dc'
			}
		};
	},
	onLoad: function (option) {
			uni.request({
				url:"http://localhost:4523/m1/5010181-4669608-default/bulletin/bulletinCheck?id",
				data:this.bulletinList,
				method:"GET",
				success:(rep)=>{
					//console.log(rep)
					//this.bulletinList=rep.data
					//console.log(this.bulletinList)
				}
			}),
			
			//初始化获取通知列表
			this.bulletinList = [
				{
					id: 1,
					date: '12-25 18:58',
					bulletinText: '我们将于明天凌晨2:00进行系统升级。升级期间，系统可能会出现短暂不可用的情况。请提前保存您的工作，以避免数据丢失。感谢您的理解与支持。',
					bulletinTitle:'系统升级通知',
				},
				{
					id: 2,
					date: '01-25 13:58',
					bulletinText: '我们重视您的意见和建议！请在应用内提交您的反馈或建议，帮助我们改进产品。我们将定期审核并考虑用户反馈，以提升您的使用体验。',
					bulletinTitle:'用户反馈征集',
				},
				{
					id: 3,
					date: '03-25 13:58',
					bulletinText: '我们刚刚发布了新版本的应用程序，包含了性能优化和一些新功能。请前往应用商店更新到最新版本，以享受更好的使用体验。',
					bulletinTitle:'应用更新通知',
				},
				{
					id: 4,
					date: '06-20 13:58',
					bulletinText: '我们很高兴地宣布，本次更新带来了全新的树洞功能，包含匿名发帖和匿名聊天等！请更新到最新版本，探索这些令人兴奋的新功能，提升您的使用体验。',
					bulletinTitle:'新功能上线',
				}
			]
	},
	methods: {
		backtoinfo()
		{
			uni.switchTab({
				url:"/pages/info/infopage"
			})
			
		},
		
		// 跳转到通知详情
		toAllReply(index) {
			uni.setStorage({
				key:"sss",
				data:this.bulletinList[index].id
			})
			console.log(this.bulletinList)
			uni.navigateTo({
				url: '/pages/info/msg/bulletin/bulletinCheck'
			});
		},
	}
};
</script>

<style lang="scss" scoped>
.bulletin {
	background-color:#ffffff ;
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
			.date {
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
