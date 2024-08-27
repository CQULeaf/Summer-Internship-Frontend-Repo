<template>
	<view>
		<view v-model="this.user">
			<view class="u-demo-wrap">
				<view class="u-demo-area">
					<u-toast ref="uToast"></u-toast>
					<view class="u-avatar-wrap">
						<image class="u-avatar-demo" v-if="this.user.avatar" :src="this.user.avatar"></image>
						<view class="u-p-b-20">{{this.user.nickname}}</view>
						<u-button  shape="circle" size="medium" @click="follow">{{this.followText}}</u-button>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				user: {
					avatar: '',
					nickname: '1111',
					username:'',
					userId:0,
				},
				show: true,
				// 定义一个变量
				logined: true,
				list: '',
				followSta:false,
				followText:'关注',
				swiperCurrent: 0,
				dataCache: {},
				loading: false,
				page: 1, // 当前页码
				
				//用户关注信息
				followList:{
					userId:'',
					followableId:'',
					isFollow:'',
					followableType:"user"
				}
			};
		},

		onShow() {
			//获取用户信息
			uni.getStorage({
				key:"userPost",
				success: (res) =>{
					this.user=res.data
					uni.getStorage({
						key:"nowAccount",
						success: (res2) => {
							const judge={
								userId:res2.data.data.userId,
								followableId:this.user.userId,
								followableType:"user"
							}
							uni.request({
								url:"http://localhost:1234/user/isFollowing",
								data:judge,
								success: (Sta) => {
									this.followSta=Sta.data.data
									//获取用户关注信息
									if(this.followSta==false){
										this.followText="关注"
									}else{
										this.followText="已关注"
									}
								}
							})
						}
					})
				}
			})
			
		},
		
		methods: {
			follow() {
			    this.followSta = !this.followSta;
			    this.followText = this.followSta ? "已关注" : "关注";
				
			    uni.getStorage({
			        key: "nowAccount",
			        success:(res) => {
						this.followList.userId=res.data.data.userId
						this.followList.followableId=this.user.userId
						this.followList.isFollow=this.followSta
			            uni.request({
			                url: "http://localhost:1234/user/followOrUnfollow",
			                data: this.followList,
			                method: "POST",
							header: {
							    "content-type": "application/x-www-form-urlencoded"
							},
			                success:(res2)=>{
			                    console.log(res2.data.data);
			                },
			            });
			        },
			        fail: (err) => {
			            console.error('Storage get failed:', err);
			        }
			    });
			},
		}
	};
</script>

<style lang="scss">
	.myinfo {
		display: flex;
		align-items: center;
		padding: 50rpx;
		background-image: linear-gradient(to top left, #ffcbcc, #ffe4e6);
	}
	.grid{
		background-color: #ffcbcc;
	}

	.custom-style {
		color: #606266;
		width: 400rpx;
	}

	.myavatar {
		width: 150rpx;
		height: 150rpx;
		border-radius: 50%;
		margin-right: 50rpx;
	}

	.grid-text {
		font-size: 32rpx;
		margin-top: 4rpx;
		color: $u-type-info;
	}

	.mynickname {
		font-size: 28rpx;
		color: #333;
	}

	page {
		background-color: #ffffff;
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

	.u-avatar-demo {
		width: 150rpx;
		height: 150rpx;
		border-radius: 100rpx;
	}

	.u-avatar-wrap {
		margin-top: 80rpx;
		overflow: hidden;
		margin-bottom: 80rpx;
		text-align: center;
	}
</style>