<template>
	<view>
		<text>设置111</text>
		<button @click="gotop">去我的页面</button>
	</view>
	
</template>

<script>
	export default {
		methods:{
			gotop(){
				uni.navigateTo({
					url:'/pages/me/concern'
				})
			}
		}
	}
</script>

<style>
</style>