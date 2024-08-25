<template>
	<view>
		<view>
			<u-navbar class="wrap" height=60 title="远方来信" title-size=40 :background="background" is-back=true
				:customBack="gotopofile">
			</u-navbar>
		</view>
			<scroll-view class="scroll-view" scroll-y @scrolltolower="onreachBottom">
				<view class="subinfolist" @click="gotoreply(index)" v-for="(subinfo, index) in subinfolist"
					:key="index">
					<image class="image" :src="subinfo.image"></image>
					<text class="text">{{ subinfo.text }}</text>
				</view>
			</scroll-view>
		<view class="floating-button">  
		    <view @click="handleClick" class="hoverbutton" ><image class="button-image" src="/static/hoverbutton.svg" ></image></view>
		</view>
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
						image: '/static/mail.svg',
						text: '不会有人再像她一样爱我了...',
					},
					{
						image: '/static/mail.svg',
						text: '我是全世界最聪明的小女孩！',
					},
					{
						image: '/static/mail.svg',
						text: '我今天被老师表扬了好开心>_<'
					},
					{
						image: '/static/mail.svg',
						text: '哈哈光顾着抽风，忘记抽你啦'
					},
					{
						image: '/static/mail.svg',
						text: '她今天跟我说话了嘿嘿...'
					},
					{
						image: '/static/mail.svg',
						text: '救命！！遇到下头男了...'
					},
					{
						image: '/static/mail.svg',
						text: '我真的要失去她了...'
					},
					{
						image: '/static/mail.svg',
						text: '原来一切都有迹可循...'
					}
				]
			}
		},

		methods: {
			gotopofile() {
				uni.navigateTo({
					url: "/pages/info/treecave/treecave"
				})
			},
			gotoreply(){
				uni.navigateTo({
					url:"/pages/home/reply"
				})
			},
			handleClick()
			{
				uni.navigateTo({
					url:"/pages/info/treecave/treepostpage"
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
		padding: 25px;
		background-color: #fafff7;
		border-bottom: 1px solid #e7f0e6;
		/* 2px 是边框宽度 */
		
	}
	.image {
		width: 100rpx;
		height: 100rpx;
		border-radius: 10%;
		margin-top: 3px;
		margin-right: 22px;
		margin-left: 1px;
	}
	
	.text {
		font-size: 16px;
		color: #333;
	}
.floating-button {  
  position: fixed;  
  bottom: 50px; /* 距离页面底部20px */  
  right: 25px; /* 距离页面右侧20px */  
  z-index: 1000; /* 确保按钮在其他内容之上 */  
}  
.button-image {  
  width: 60px;  
  height: 60px;  
  /* 如果你不想图片被拉伸，可以使用aspectFit等mode值 */ 

}  
.hoverbutton{
	background-color: rgba(255, 255, 255, 0);
}
</style>