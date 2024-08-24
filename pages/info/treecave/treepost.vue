<template>
	<view>
		<view>
			<u-navbar class="wrap" height=60 title="远方来信" title-size=40 :background="background" is-back=true :customBack="gotopofile">
			</u-navbar>
		</view>
		<view>页面建设中1</view>
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
									}
				}
			},
			
			methods: {
		gotopofile(){
			uni.navigateTo({
				url:"/pages/info/treecave/treecave"
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
</style>