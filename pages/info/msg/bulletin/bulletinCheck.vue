<template>
	<view class="wrap">
		<view class="bulletin">
			<view class="top">
				<u-navbar :is-back="true" title="详情" title-size=35 :customBack="backtoinfo"
					height="55">
				</u-navbar>
			</view>
			<view class="nav_title">{{ this.bulletinList[this.id-1].bulletinTitle }}</view>
			<view class="left">
				<view class="user-info">
					<view class="date">06-25 13:58</view>
				</view>
			</view>
			<!-- <u-line color="#c8c8c8"/> -->
			<view class="content">{{ this.bulletinList[this.id-1].bulletinText }}</view>
		</view>
		<u-divider>看完了</u-divider>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				bulletinList: [],
				index: '',
				id:''
			};
		},
		onLoad() {
			this.getReply();
		},
		methods: {
			backtoinfo() {
				uni.navigateTo({
					url: "/pages/info/msg/bulletin/bulletin"
				})

			},
			// 回复列表
			getReply() {
				this.bulletinList = [{
						id: 1,
						date: '12-25 18:58',
						bulletinText: '我们将于明天凌晨2:00进行系统升级。升级期间，系统可能会出现短暂不可用的情况。请提前保存您的工作，以避免数据丢失。感谢您的理解与支持。',
						bulletinTitle: '系统升级通知',
					},
					{
						id: 2,
						date: '01-25 13:58',
						bulletinText: '我们重视您的意见和建议！请在应用内提交您的反馈或建议，帮助我们改进产品。我们将定期审核并考虑用户反馈，以提升您的使用体验。',
						bulletinTitle: '用户反馈征集',
					},
					{
						id: 3,
						date: '03-25 13:58',
						bulletinText: '我们刚刚发布了新版本的应用程序，包含了性能优化和一些新功能。请前往应用商店更新到最新版本，以享受更好的使用体验。',
						bulletinTitle: '应用更新通知',
					},
					{
						id: 4,
						date: '06-20 13:58',
						bulletinText: '我们很高兴地宣布，本次更新带来了全新的树洞功能，包含匿名发帖和匿名聊天等！请更新到最新版本，探索这些令人兴奋的新功能，提升您的使用体验。',
						bulletinTitle: '新功能上线',
					}
				]
			}
		},
		onShow() {
			uni.getStorage({
				key:"sss",
				success: (res) => {
					this.id=res.data
					console.log(this.id)
				}
			})
		}
	};
</script>

<style lang="scss" scoped>
	page {
		background-color: #f2f2f2;
	}

	.nav_title {
		padding: 15px 15px 12px 12px;
		font-size: 20px;
		font-weight: 400;
		color: #000000;
		border: 0;
		background: #ffffff;
		outline: none;
	}

	.content {
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