<template>
	<view class="wrap">
		<view class="comment">
			<view class="top">
				<view class="left" @click="gotoUserPage">
					<view class="heart-photo"><image :src="user.avatar" mode=""></image></view>
					<view class="user-info">
						<view class="name">{{ user.nickname }}</view>
						<view class="date">{{comment.createdAt}}</view>
					</view>
				</view>
				<view class="right" :class="{ highlight: comment.isLike }">
					{{ comment.likeCount }}
					<u-icon v-if="!comment.isLike" name="thumb-up" class="like" color="#9a9a9a" :size="30" @click="getLike"></u-icon>
					<u-icon v-if="comment.isLike" name="thumb-up-fill" class="like" :size="30" @click="getLike"></u-icon>
				</view>
			</view>
			<view class="content u-font-40">{{ comment.title}}</view>
			<view class="content u-p-t-30" >{{ comment.postContent }}</view>
			<image v-if="comment.cover" :src="comment.cover" mode=""></image>
		</view>
		
		<!-- 回复 -->
		<view class="all-reply">
			<view class="all-reply-top">全部回复</view>
			<view class="item" v-for="(item, index) in commentList" :key="index">
				<view class="comment">
					<view class="top">
						<view class="left">
							<view class="heart-photo" @click="gotoUserPage2(item)"><image :src="item.avatar" mode=""></image></view>
							<view class="user-info">
								<view class="name">{{ item.nickname }}</view>
								<view class="date">{{ item.createdAt }}</view>
							</view>
						</view>
						<view class="right"  :class="{ highlight: item.isLike }">
							<view class="num">{{ item.likeCount }}</view>
							<u-icon v-if="!item.isLike" name="thumb-up" class="like" :size="30" color="#9a9a9a" @click="getLike(index)"></u-icon>
							<u-icon v-if="item.isLike" name="thumb-up-fill" class="like" :size="30" @click="getLike(index)"></u-icon>
						</view>
					</view>
					<view class="content">{{ item.content }}</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
import colorGradient from '../../uview-ui/libs/function/colorGradient';
export default {
	data() {
		return {
			user:{
				userId:'',
				avatar:'',
				nickname:''
			},
			comment:{
				userId:'',
				nickname: '',
				date: '',
				postContent: '',
				title:'',
				cover:'',
				likeCount:'',
				isLike:'',
				},
			commentList: [{
				avatar:'',
				nickname:'',
				userId:''
			}],
		};
	},
	onLoad() {
		this.getComment();
	},
	
	onShow(){
	},
	
	methods: {
		getComment(){
			uni.getStorage({
				key: 'postData',
				success:(res) => {
					this.comment=res.data
					//console.log(this.comment.commentCount);
					
					// 请求获得所有的回复数据
					uni.request({
						url:"http://localhost:1234/home/getReply",
						data:res.data,
						success:(respones) =>{
							//console.log(respones);
							this.commentList=respones.data.data
							console.log(this.commentList);
							for(let key in this.commentList) {
							    uni.request({
							    	url:"http://localhost:1234/user/getUserInfo",
									data:this.commentList[key],
									success:(res2) => {
										//console.log(res2.data);
										this.commentList[key].avatar=res2.data.data.avatar
										this.commentList[key].nickname=res2.data.data.nickname
										this.commentList[key].userId=res2.data.data.userId
									},
							    })
							}
						}
					})
				}
			});
			
			uni.request({
				url:"http://localhost:1234/user/getUserInfo",
				data:this.comment,
				success:(res)=>{
					this.user=res.data.data
					uni.setStorage({
						key:"userPost",
						data:res.data.data
					})
				},
			})
		},
		
		gotoUserPage(){
			uni.getStorage({
				key:"nowAccount",
				success:(res)=>{
					if(res.data.data.userId!=this.comment.userId){
						uni.navigateTo({
							url:"/pages/userPage"
						})
					}else{
						uni.navigateTo({
							url:"/pages/me/myinfo/Information"
						})
					}
				},
			})
		},
		
		
		// 跳转到评论页面
		gotoUserPage2(userId){
			console.log(userId)
			uni.request({
				url:"http://localhost:1234/user/getUserInfo",
				data:userId,
				success:(res)=>{
					console.log(userId);
					uni.setStorage({
						key:"userPost",
						data:res.data.data,
						success() {
							console.log(res);
							uni.getStorage({
								key:"nowAccount",
								success:(res)=>{
									if(res.data.data.userId!=res.userId){
										uni.navigateTo({
											url:"/pages/userPage"
										})
									}else{
										uni.navigateTo({
											url:"/pages/me/myinfo/Information"
										})
									}
								},
							})
						}
					})
				},
			})
		},
		// 点赞
		getLike(index) {
			if (index === 0 || index > 0) {
				this.commentList[index].isLike = !this.commentList[index].isLike;
				if (this.commentList[index].isLike == true) {
					this.commentList[index].likeCount++;
				} else {
					this.commentList[index].likeCount--;
				}
			} else {
				if (this.comment.isLike == true) {
					this.comment.isLike = !this.comment.isLike;
					this.comment.likeCount--;
				} else {
					this.comment.isLike = !this.comment.isLike;
					this.comment.likeCount++;
				}
			}
		},
	}
};
</script>

<style lang="scss" scoped>
page {
	background-color: #f2f2f2;
}
.comment {
	padding: 30rpx;
	font-size: 32rpx;
	background-color: #ffffff;
	.top {
		display: flex;
		justify-content: space-between;
	}
	.left {
		display: flex;
		.heart-photo {
			image {
				width: 64rpx;
				height: 64rpx;
				border-radius: 50%;
				background-color: #f2f2f2;
			}
		}
		.user-info {
			margin-left: 10rpx;
			.name {
				color: #5677fc;
				font-size: 28rpx;
				margin-bottom: 4rpx;
			}
			.date {
				font-size: 20rpx;
				color: $u-light-color;
			}
		}
	}
	.right {
		display: flex;
		font-size: 20rpx;
		align-items: center;
		color: #9a9a9a;
		.like {
			margin-left: 6rpx;
		}
		.num{
			font-size: 26rpx;
			color: #9a9a9a;
		}
	}
	.highlight {
		color: #5677fc;
		.num{
			color: #5677fc;
		}
	}
}
.all-reply {
	margin-top: 10rpx;
	padding-top: 20rpx;
	background-color: #ffffff;
	.all-reply-top {
		margin-left: 20rpx;
		padding-left: 20rpx;
		border-left: solid 4rpx #5677fc;
		font-size: 30rpx;
		font-weight: bold;
	}
	.item {
		border-bottom: solid 2rpx $u-border-color;
	}
}
</style>
