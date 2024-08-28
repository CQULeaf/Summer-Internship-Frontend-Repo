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
			
			<!-- 一句话介绍 -->
			<u-form-item label="一句话介绍" label-width="150">
				<u-input placeholder="请输入您的一句话介绍" type="text" v-model="user.headline"></u-input>
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
				<u-input placeholder="请输入您的手机号码" type="text" v-model="user.phoneNumber"></u-input>
			</u-form-item>
			
			<!-- 专业 -->
			<u-form-item label="专业" label-width="150">
				<u-input placeholder="请输入您的专业" type="text" v-model="user.major"></u-input>
			</u-form-item>
			
			<!-- mbti -->
			<u-form-item label="mbti" label-width="150">
				<u-input placeholder="请输入您的mbti" type="text" v-model="user.mbti"></u-input>
			</u-form-item>

			<!-- 性别 -->
			<view class="u-m-t-20">
				<view class="u-config-item">
					<view class="u-item-title">性别</view>
					<u-subsection :current='current' :list="['男', '女']" @change="statusChange"></u-subsection>
				</view>
			</view>
			
			<!-- 家乡 -->
			<view class = "u-item-title u-m-t-10" >
				<u-picker v-model="hometownshow" mode="region" @confirm="confirm"></u-picker>
				<u-cell-item title="家乡" :arrow="true" v-model="user.hometown" @click="hometownshow = true">
				</u-cell-item>
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
					userId:1,
					username:"1",
					email:"1",
					phoneNumber:"1",
					createdAt:"1",
					updatedAt:"1",
					status:"1",
					nickname:"1",
					avatar:'',
					bio:"1",
					gender:'',
					hometown:''
				},
				hometownshow:false,
				current:0
			}

		},
		
		created() {
					// 监听从裁剪页发布的事件，获得裁剪结果
					uni.$on('uAvatarCropper', path => {
						this.user.avatar = path;
						// 可以在此上传到服务端
						uni.uploadFile({
							url: 'http://www.example.com/upload',
							filePath: path,
							name: 'file',
							complete: (res) => {
								console.log(res);
							}
						});
					})
				},

		methods: {
			submit(){		
				uni.request({
					url:'http://localhost:8080/user/updateInfo',
					data:this.user,
					method:"POST",
					header:{
						'Content-Type': 'application/json'
					},
					success: (res) => {
						const value = uni.getStorageSync('nowAccount')
						value.data.data=this.user
						value.data.code=res.code
						value.data.msg=res.data.msg
						console.log(res)
						
						uni.setStorageSync('nowAccount', value);
					}
				})
				const value = uni.getStorageSync('nowAccount');
				console.log(value)
				value.data=this.user
				uni.setStorageSync('nowAccount', value);
				uni.redirectTo({
					url:"/pages/me/myinfo/Information",
				})
			},
			
			onShow(){
				const value = uni.getStorageSync('nowAccount');
				this.user=value.data
				console.log(this.user)
				this.current=this.user.gender=='男' ? 0:1
			},
			
			statusChange(index){	
				this.user.gender = (index == 0 ? '男' : '女')
			},

			confirm(index){
				console.log(this.user)
				this.user.hometown=index.province.label+index.city.label+index.area.label
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
