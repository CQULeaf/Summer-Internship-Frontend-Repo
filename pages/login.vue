<template>
	<view>
		<u-form :model="user">
			<div class="container">
				<div class="login-wrapper">
					<div class="header">登录</div>
					<div class="form-wrapper">
						<input type="text" name="username" placeholder="请输入用户名" class="input-item" v-model="user.username">
						<input type="password" name="password" placeholder="请输入密码" class="input-item" v-model="user.password">
						<div class="btn" @click="login">登录</div>
						<div class="btn" @click="register">注册</div>
					</div>
				</div>
			</div>
		</u-form>
	</view>
</template>

<script>
    export default {
		data()
		{
			return{
				user:{
					username:"",
					password:""
				},
				account:''
			}
		},
		
		methods:{
			login:function(){
				console.log(this.user)
				uni.request({
					url:'http://localhost:8080/user/login',
					data:this.user,
					method:"POST",
					header:{
						'Content-Type': 'application/json'
					},
					success: (res) => {
						uni.setStorageSync('nowAccount', res.data);
					}
				});
				const value = uni.getStorageSync('nowAccount');
				console.log(value)
				
				if (value.code===200){
					uni.switchTab({
						url: '/pages/me/mypage'
					});
				};
			},
			
			register(){
				console.log("注册")
				uni.navigateTo({
					url:"/pages/register"
				})
			}
		}
    }
</script>

<style scoped>

html {
    height: 100%;
}
body {
    height: 200%;
}
.container {
    height: 100vh;
    width: 100%;
    background-color: #fed6dc;
}
.login-wrapper {
    background-color: #fff;
    width: 480rpx;
    height: 1050rpx;
    border-radius: 15rpx;
    padding: 0 50px;
    position: absolute;
    left: 50%;
    top: 49%;
    transform: translate(-50%, -50%);
}
.header {
    font-size: 85rpx;
    font-weight: normal;
    text-align: center;
    line-height: 200px;
	color: #adaba8;
}
.input-item {
    display: block;
    width: 90%;
    margin-bottom: 20px;
    border: 0;
    padding: 10px;
    border-bottom: 1px solid rgb(164, 165, 155);
    font-size: 15px;
    outline: none;
}
.input-item:placeholder {
    text-transform: uppercase;
}
.btn {
    text-align: center;
    padding: 10px;
    margin: 0 auto;
    width: 90%;
    margin-top: 45px;
	margin-bottom: -30px;
    background-color: #fecdd4;
    color: #fff;
}
.msg {
    text-align: center;
    line-height: 88px;
}
a {
    text-decoration-line: none;
    color: #fed6dc;
}

</style>
