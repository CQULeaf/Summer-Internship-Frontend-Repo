<template>
	<view>
		<u-navbar :background="background" title-color="#fff" title="系统公告"></u-navbar>
		<view class="bulletin" v-for="(res, index) in bulletinList" :key="res.id">
			<view class="right">
				<view class="content">{{ res.bulletinTitle }}</view>
				<view class="top">
					<view class="nickname">{{ res.nickname }}</view>
					<view class="date">{{ res.date }}</view>
				</view>
				<view class="u-line-2">{{ res.bulletinText }}</view>
				<u-cell-item class="u-p-l-2" title="详情" @tap="toAllReply"></u-cell-item>
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
			background: {
				'background-image': 'linear-gradient(45deg, rgb(28, 187, 180), rgb(141, 198, 63))'
			},
		};
	},
	onLoad: function (option) {
			uni.request({
				url:"http://127.0.0.1:4523/m1/5010181-4669608-default/bulletin/bulletinCheck?id",
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
					nickname: '叶轻眉',
					date: '12-25 18:58',
					bulletinText: '我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的',
					bulletinTitle:'不敢相信',
				},
				{
					id: 2,
					nickname: '叶轻眉1',
					date: '01-25 13:58',
					bulletinText: '我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的',
					bulletinTitle:'不敢相信',
				},
				{
					id: 3,
					nickname: '叶轻眉2',
					date: '03-25 13:58',
					bulletinText: '我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的',
					bulletinTitle:'不敢相信',
				},
				{
					id: 4,
					nickname: '叶轻眉3',
					date: '06-20 13:58',
					bulletinText: '我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的',
					bulletinTitle:'不敢相信',
				}
			]
	},
	methods: {
		// 跳转到通知详情
		toAllReply() {
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
