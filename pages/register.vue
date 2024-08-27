<template>
    <div class="container">
		<u-form :model="user">
            <div class="login-wrapper">
                <div class="header">注册</div>
                <div class="form-wrapper">
                    <input type="text" name="username" placeholder="账户" class="input-item" v-model="user.username">
                    <input type="password" name="password" placeholder="密码" class="input-item" v-model="user.password1">
                    <input type="password" name="repassword" placeholder="再次确认密码" class="input-item" v-model="user.password2">
                    <div class="btn" @click="register">注册</div>
					<view class="alredy"  @click="backtologin">我已经注册账号</view>
                </div>
            </div>
		</u-form>
     </div>    
</template>
    
<script>
    export default {
		data()
		{
			return{
				user:{
					username:"",
					password1:"",
					password2:"",
				}
			}
		},
		methods:{
			register(){
				console.log(this.user)
				if(this.user.username==''){
					this.$u.toast("注册失败，请输入用户名")
					return
				}
				if(this.user.password1==''){
					this.$u.toast("注册失败，请输入密码")
					return
				}
				uni.request({
					url:"http://localhost:1234/user/register",
					data:this.user,
					method:'POST',
					success: (res) => {
						console.log(res)
						if(res.data.code==200)
						{
							uni.redirectTo({
								url: '/pages/login'
							});
						}
						else
						{
							this.$u.toast("注册失败，请更换用户名哦")
						}
					}
				})
			},
			
			backtologin(){
				uni.navigateTo({
					url:'/pages/login'
				})
			}
		}
    }
</script>
 
<style scoped>
.body {
    height: 200%;
}
.container {
    /* margin-top: 5%;  */
    height: 100vh;
    width: 100%;
    background-color: #fed6dc;
}
.login-wrapper {
    background-color: #fff;
    width: 400rpx;
    height: 1100rpx;
    border-radius: 15rpx;
    padding: 0 50px;
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
}
.header {
    font-size: 80rpx;
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
    margin-top: 35px;
    background-color: #fecdd4;
    color: #fff;
}
.msg {
    text-align: center;
    line-height: 88px;
}
.alredy {
    text-decoration-line: none;
    color: #d6adb4;
	padding: 5px;

}

</style>
