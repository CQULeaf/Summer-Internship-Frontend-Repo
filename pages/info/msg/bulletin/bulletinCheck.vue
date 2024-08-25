<template>
	<view class="wrap">
		<view class="bulletin">
			<view class="top">
				
			</view>
			<view class="nav_title">{{ bulletin.bulletinTitle }}</view>
			<view class="left">
				<view class="user-info">
					<view class="date">06-25 13:58</view>
				</view>
			</view>
			<!-- <u-line color="#c8c8c8"/> -->
			<view class="content">{{ bulletin.bulletinText }}</view>
		</view>
		<u-divider>看完了</u-divider>
	</view>
</template>

<script>
export default {
	data() {
		return {
			bulletin: ''
		};
	},
	onLoad() {
		this.getReply();
	},
	methods: {

		// 回复列表
		getReply() {
			this.bulletin = {
				id: 1,
				name: '叶轻眉',
				date: '12-25 18:58',
				bulletinTitle: '我不信伊朗会没有后续反应，美国肯定会为今天的事情付出代价的哈基米 哈基米 哈基米 哈基米哈基米 哈基米 哈基米 哈基米',
				bulletinText: '哈基米 哈基米 哈基米 哈基米  哈基米 哈基米 我那咩鲁拖 啊西嘎 啊西嘎 啊西嘎 啊西嘎 啊西嘎 哈呀库 那录  哈基米 哈基米 哈基米 哈基米  哈基米 哈基米 我那咩哈基米 哈基米 哈基米 哈基米  哈基米 哈基米 我那咩鲁拖 啊西嘎 啊西嘎 啊西嘎 啊西嘎 啊西嘎 哈呀库 那录  哈基米 哈基米 哈基米 哈基米  哈基米 哈基米 我那咩哈基米 哈基米 哈基米 哈基米  哈基米 哈基米 我那咩鲁拖 啊西嘎 啊西嘎 啊西嘎 啊西嘎 啊西嘎 哈呀库 那录  哈基米 哈基米 哈基米 哈基米  哈基米 哈基米 我那咩哈基米 哈基米 哈基米 哈基米  哈基米 哈基米 我那咩鲁拖 啊西嘎 啊西嘎 啊西嘎 啊西嘎 啊西嘎 哈呀库 那录  哈基米 哈基米 哈基米 哈基米  哈基米 哈基米 我那咩哈基米 哈基米 哈基米 哈基米  哈基米 哈基米 我那咩鲁拖 啊西嘎 啊西嘎 啊西嘎 啊西嘎 啊西嘎 哈呀库 那录  哈基米 哈基米 哈基米 哈基米  哈基米 哈基米 我那咩哈基米 哈基米 哈基米 哈基米  哈基米 哈基米 我那咩鲁拖 啊西嘎 啊西嘎 啊西嘎 啊西嘎 啊西嘎 哈呀库 那录  哈基米 哈基米 哈基米 哈基米  哈基米 哈基米 我那咩',
				url: 'https://cdn.uviewui.com/uview/template/SmilingDog.jpg',
			};
		}
	}
};
</script>

<style lang="scss" scoped>
page {
	background-color: #f2f2f2;
}

.nav_title{
    padding: 15px 15px 12px 12px;
    font-size: 20px;
    font-weight: 400;
    color: #000000;
    border: 0;
    background: #ffffff;
    outline: none;
  }
  
.content{
    padding: 15px 15px 12px 12px;
    font-size: 15px;
    font-weight: 400;
    color: #000000;
    border: 0;
    background: #ffffff;
    outline: none;
  }

.bulletin {
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
			margin-left: 28rpx;
			.date {
				font-size: 20rpx;
				color: $u-light-color;
			}
		}
	}
}
</style>
