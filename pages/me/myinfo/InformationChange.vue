<template>
	<view :model="user" >
		<view class="u-flex user-box u-p-l-30 u-p-r-20 u-p-b-20" @click="chooseAvatar" @uAvatarCropper="uAvatarCropper">
			<view class="u-m-r-10">
				<u-avatar :src="user.avatar" size="140">
					<image @tap="chooseAvatar" class="u-avatar-demo" v-if="user.avatar" :src="user.avatar" mode="aspectFill"></image>
				</u-avatar>
			</view>
			<view class="u-flex-1">
				<view class="u-font-18 u-p-b-20">修改头像</view>
			</view>
		</view>

		<u-form>

			<!-- 姓名 -->
			<u-form-item label="姓名" label-width="150">
				<u-input placeholder="请输入姓名" type="text" v-model="user.nickname"></u-input>
			</u-form-item>

			<!-- 个人简介 -->
			<u-form-item label="个人简介" label-width="150">
				<u-input placeholder="请输入您的个人简介" type="text" v-model="user.bio"></u-input>
			</u-form-item>

			<!-- 电子邮箱 -->
			<u-form-item label="电子邮箱" label-width="150">
				<u-input placeholder="请输入您的电子邮箱" type="text" v-model="user.email"></u-input>
			</u-form-item>

			<!-- 手机号码 -->
			<u-form-item label="手机号码" label-width="150">
				<u-input placeholder="请输入您的手机号码" type="text" v-model="user.phone_number"></u-input>
			</u-form-item>

			<!-- 性别 -->
			<view class="u-m-t-20">
				<view class="u-config-item">
					<view class="u-item-title">性别</view>
					<u-subsection :current='current' :list="['男', '女']" @change="statusChange"></u-subsection>
				</view>
			</view>

			<!-- 生日 -->
			<view class = "u-item-title">
				<u-picker v-model="birthdayshow" mode="time" @confirm="confirm"></u-picker>
				<u-cell-item title="生日" :arrow="true" v-model="user.birthday" @click="birthdayshow = true"></u-cell-item>
			</view>

		</u-form>
		
		<u-button hover-stay-time="150" shape="circle" type="success" @click="submit" >提交</u-button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				user:{
					id:1,
					username:"1",
					email:"1",
					phone_number:"1",
					created_at:"1",
					updated_at:"1",
					status:"1",
					nickname:"huang",
					avatar:"https://uviewui.com/common/logo.png",
					bio:"1",
					gender:'女',
					age:"20",
					birthday:"1"
				},
				birthdayshow:false,
				current:0,
			}

		},

		methods: {
			submit(){		
				uni.navigateTo({
					url:"/pages/me/myinfo/Information",
				})
			},
			
			
			onLoad: function () {
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
			
			statusChange(index){	
				this.user.gender = (index == 0 ? '男' : '女')
			},

			confirm(index){
				this.user.birthday=index.year+"年"+index.month+"月"+index.day+"日"
				console.log(this.user)
				this.user.age=2024-index.year
			},

			chooseAvatar() {
				this.$u.route({
					// 关于此路径，请见下方"注意事项"
					url: '/uview-ui/components/u-avatar-cropper/u-avatar-cropper',
					params: {
						destWidth: 300,
						rectWidth: 200,
						fileType: 'jpg',
					}
				})
			},

			uAvatarCropper(index){
				this.user.avatar=index
				console.log(index)
			},
		}
	}
</script>

<style>

</style>
