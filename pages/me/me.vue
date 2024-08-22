<template>
	<view class="container">
		<!-- 用户信息部分 -->
		<view class="myinfo">
			<image class="myavatar" :src="user.avatar"></image>
			<text class="mynickname">{{ user.nickname }}</text>
			<button size="mini" style="background-color:#ffb9c0;color: #000000" @click="goToProfile">去设置</button>
		</view>
		<!-- 我们根据某一个状态值进行不同界面切换 -->
	</view>
</template>

<script>
	export default {
		data() {
			return {
				href: 'https://uniapp.dcloud.io/component/README?id=uniui',
				user: {
					avatar: '/static/c1.png',
					nickname: '1111'
				},
				pic: 'https://uviewui.com/common/logo.png',
				show: true,
				// 定义一个变量
				logined: false
			}
		},
		onLoad() {

		},
		methods: {
			fetchUser() {
				uni.request({
					url: "http://127.0.0.1:4523/m1/5010181-4669608-default/user/me", //在fetchUserList方法中的请求URL使用了'${apiEndpoint}?page=${page}'，这会导致字符串直接拼接而非动态生成变量。正确的做法应该是使用模板字符串的语法来动态替换变量
					data:this.user,
					method: 'GET',
					success: (res) => {
						if (res.statusCode === 200) {
						  console.log(res)
							}
						else {
							uni.showToast({
								title: '获取数据失败',
								icon: 'none'
							});
						}
					},
					fail: (err) => {
						uni.showToast({
							title: '请求失败',
							icon: 'none'
						});
					},
					complete: () => {
						this.loading = false;
					}
				});
			},
			goToProfile() {
				uni.navigateTo({
					url: '/pages/me/setting'
				});
			},
			login() {
				console.log("登录")
				//跳转到登录页面
				uni.navigateTo({
					url: "/pages/me/login"
				})
			}
		}
	}
</script>

<style>
	.myinfo {
		display: flex;
		align-items: center;
		padding: 50rpx;
		background-image: linear-gradient(to top left, #ffcbcc, #ffe4e6);
	}

	.myavatar {
		width: 150rpx;
		height: 150rpx;
		border-radius: 50%;
		margin-right: 50rpx;
	}

	.mynickname {
		font-size: 28rpx;
		color: #333;
	}
</style>


<style lang="scss">
	page {
		background-color: #ededed;
	}

	.camera {
		width: 54px;
		height: 44px;

		&:active {
			background-color: #ededed;
		}
	}

	.user-box {
		background-color: #fff;
	}
</style>