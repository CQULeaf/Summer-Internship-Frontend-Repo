<template>
	<view :modle="user">
		<view>
			<view class="u-m-r-10 u-avatar-wrap">
				<image class="u-avatar-demo" :src="user.avatar"></image>
			</view>
		</view>
		
		<view class="u-m-t-20">
			<u-cell-group>
				<u-cell-item title="昵称" :arrow="false" v-model="this.user.nickname"></u-cell-item>
				<u-cell-item title="个人简介" :arrow="false" v-model="user.bio"></u-cell-item>
				<u-cell-item title="ID" :arrow="false" v-model="user.id"></u-cell-item>
				<u-cell-item title="生日" :arrow="false" v-model="user.birthday"></u-cell-item>
				<u-cell-item title="性别" :arrow="false" v-model="user.gender"></u-cell-item>
				<u-cell-item title="年龄" :arrow="false" v-model="user.age"></u-cell-item>
				<u-cell-item title="电子邮箱" :arrow="false" v-model="user.email"></u-cell-item>
				<u-cell-item title="手机号码" :arrow="false" v-model="user.phone_number"></u-cell-item>
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
					id:123,
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
		
		onLoad: function (option) { //option为object类型，会序列化上个页面传递的参数
				var hit
				this.current=this.user.gender=='男' ? 0:1
				const eventChannel = this.getOpenerEventChannel();
				eventChannel.on('acceptDataFromOpenerPage', function(data){
					hit=data.data
				})
				//this.user.id=hit.id
				console.log(hit.id)
				this.user=hit
				console.log((this.user))
		},
			
		methods: {
			InformationChange(user){//修改个人信息
				uni.navigateTo({
					url:'/pages/me/myinfo/InformationChange',
					success: function(res) {
						console.log(user)
					    res.eventChannel.emit('acceptDataFromOpenerPage',{ data: user})
					}
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
	background-color: #ffffff;
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
