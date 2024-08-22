<template>
	<view>
		<u-form :model="user">
			<u-form-item label="姓名" label-width="120">
				<u-input placeholder="请输入姓名" type="text" v-model="user.name" />
			</u-form-item>
			<u-form-item label="密码" label-width="120">
				<u-input placeholder="请输入密码" type="password" v-model="user.password" />
			</u-form-item>
			<u-form-item label="记住密码" label-width="150">
				<u-switch slot="right" v-model="user.rememberMe" />
			</u-form-item>
		</u-form>
		<u-button @click="submit">提交</u-button>
		<u-button @click="register">注册</u-button>
		<u-link :href="href">百度</u-link>
	</view>
</template>

<script>
	export default {
		data() {
			return {
			//定义一个对象，用来存储多个变量值
			//用于变量定义
			//变量名：变量值
			user:
			{zhanghao:"",
			mima:""
				
			},
			href:"http://www.baidu.com"
				
			}
		},
		methods: {
			
			
			regsiter()
			{
				console.log("注册")
				uni.navigateTo({
				url:"/pages/me/register"	
				})
			},
			submit()
			{
				//退到上一级 但是找不到
				//uni.navigateBack()
				//取数据 发到服务器之后之后才会有下面这些
				console.log(this.user)
				uni.request({
					url:"http://localhost:8080/login",
					data:this.user,
					method:"POST",//请求方式
					success:(response)=>{
						//输出结果
						console.log(response)
						//判断是否登录成功data.code
						if(response.data.code==200)
						{//登录成功 跳转到登录后的我的界面
							//存信息  数据缓存 有同步，异步
							uni.setStorageSync("userinfo",response.data,result)
							//回退
							uni.navigateBack()
						}
						else
						{//登录失败
							//提示登录失败
							this.$u.toast("登录失败")
						}
						
					}
				})
				
				
				
				
				
				
			}
			
		}
	}
</script>

<style>

</style>
