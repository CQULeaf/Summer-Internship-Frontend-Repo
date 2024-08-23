<template>
	<view>
		<u-navbar :is-back="false" title="" :background="background">
			
		</u-navbar>
		<view class="content">
			<!-- 正文内容 -->
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				background: {
					backgroundColor: '#001f3f',
					
					// 导航栏背景图
					 background: 'url(https://cdn.uviewui.com/uview/swiper/1.jpg) no-repeat',
					// 还可以设置背景图size属性
					 backgroundSize: 'cover',
					
					// 渐变色
					 backgroundImage: 'linear-gradient(45deg, rgb(28, 187, 180), rgb(141, 198, 63))'
				}
			}
		}
	}
</script>