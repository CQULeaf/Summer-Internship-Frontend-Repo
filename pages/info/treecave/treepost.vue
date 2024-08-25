<template>
	<view>
		<view>
			<u-navbar class="wrap" height=60 title="远方来信" title-size=40 :background="background" is-back=true
				:customBack="gotopofile">
			</u-navbar>
		</view>
			<scroll-view class="scroll-view" scroll-y @scrolltolower="onreachBottom">
				<view class="subinfolist" @click="gotopofile(index)" v-for="(subinfo, index) in subinfolist"
					:key="index">
					<image class="image" :src="subinfo.image"></image>
					<text class="text">{{ subinfo.text }}</text>
				</view>
			</scroll-view>
	</view>
</template>

<script>
	export default {
		data() {
			return {

				background: {
					backgroundColor: '#001f3f',
					backgroundSize: 'cover',
					backgroundImage: 'linear-gradient(45deg, rgb(159, 219, 196),rgb(139, 219, 186),rgb(35, 187, 154))'
				},
				subinfolist: [
					// 标签数据
					{
						image: '../../static/liked.png',
						text: '点赞',
					},
					{
						image: '../../static/comment.svg',
						text: '评论',
					},
					{
						image: '../../static/announcement.svg',
						text: '公告'
					}
				]
			}
		},

		methods: {
			gotopofile() {
				uni.navigateTo({
					url: "/pages/info/treecave/treecave"
				})
			}
		}
	}
</script>

<style>
	.wrap {
		display: flex;
		/* 使用 flex 布局 */
		align-items: center;
		/* 水平居中 */
		padding: 0 110rpx;
		/* 背景颜色 */
		border: 2px solid #2fa787;
		/* 边框 */
		border-radius: 5px;
		/* 圆角 */
		box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
		/* 阴影效果 */
	}
	.scroll-view {
		width: 100%;
		height: 100%;
		/* 确保 scroll-view 填满 swiper-item */
		background-color: #fffdff;
	}
	.subinfolist {
		display: flex;
		align-items: center;
		padding: 10px;
		background-color: #fafff7;
		border-bottom: 1px solid #f0dddf;
		/* 2px 是边框宽度 */
	
	}
	.image {
		width: 100rpx;
		height: 100rpx;
		border-radius: 10%;
		margin-top: 3px;
		margin-right: 20px;
		margin-left: 10px;
	}
	
	.text {
		font-size: 16px;
		color: #333;
	}
</style>