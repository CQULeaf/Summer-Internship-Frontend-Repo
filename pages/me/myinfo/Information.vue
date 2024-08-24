<template>
	<view :modle="user">
		<view>
			<view class="u-m-r-10 u-avatar-wrap">
				<image class="u-avatar-demo" :src="user.avatar"></image>
				<view>{{this.user.nickname}}</view>
			</view>
		</view>
		
		<view class="u-m-t-20">
			<u-cell-group>
				<u-cell-item title="一句话介绍" :arrow="false" v-model="this.user.headline"></u-cell-item>
			</u-cell-group>
			<u-cell-group class="u-m-t-20">
				<view>
					<u-cell-item title="ID" :arrow="false" v-model="user.userId"></u-cell-item>
					<u-cell-item title="性别" :arrow="false" v-model="user.gender"></u-cell-item>
					<u-cell-item title="电子邮箱" :arrow="false" v-model="user.email"></u-cell-item>
					<u-cell-item title="手机号码" :arrow="false" v-model="user.phoneNumber"></u-cell-item>
				</view>
			</u-cell-group>
			<u-cell-group class="u-m-t-20">
				<view>
					<u-cell-item title="个人简介" :arrow="false" v-model="user.bio"></u-cell-item>
					<u-cell-item title="家乡" :arrow="false" v-model="user.hometown"></u-cell-item>
					<u-cell-item title="专业" :arrow="false" v-model="user.major"></u-cell-item>
					<u-cell-item title="mbti" :arrow="false" v-model="user.mbti"></u-cell-item>
				</view>
			</u-cell-group>
		</view>
		
		<view class="u-m-t-20">
			<u-cell-group>
				<u-cell-item icon="setting" title="修改个人信息" @click="InformationChange(user)"></u-cell-item>
			</u-cell-group>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				user:{
					userId:123,
					username:"",
					email:"6666666@qq.com",
					phone_number:"15252525252",
					created_at:"",
					updated_at:"",
					status:"http",
					nickname:"HJJ",
					avatar:"https://uviewui.com/common/logo.png",
					bio:"HJJ666",
					gender:"男",
					age:"20",
					birthday:"1111111"
				}
			}
		},
		
		onShow(){
			const value = uni.getStorageSync('nowAccount');
			this.user=value.data
		},
			
		methods: {
			InformationChange(user){//修改个人信息
				uni.navigateTo({
					url:'/pages/me/myinfo/InformationChange'
				})
				
			},

			// 图片预览
			preAvatar() {
				wx.previewImage({
					current: '', // 当前显示图片的 http 链接
					urls: [this.user.avatar] // 需要预览的图片 http 链接列表
				})
			},
		}
	}
</script>

<style lang="scss">
page{
	background-color: #efefef;
}

.u-avatar-wrap {
	margin-top: 80rpx;
	overflow: hidden;
	margin-bottom: 80rpx;
	text-align: center;
}

	.u-avatar-demo {
		width: 150rpx;
		height: 150rpx;
		border-radius: 100rpx;
	}

.camera{
	width: 54px;
	height: 44px;
	
	&:active{
		background-color: #ffffff;
	}
}
.user-box{
	background-color: #ffffff;
}
</style>
