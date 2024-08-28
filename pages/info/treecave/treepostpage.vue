<template>
	<view class="help-container">
		<u-navbar :is-back="true" title-size=40 title="你的秘密" :background="background" :customBack="backtotreepost" height="65">
			<div class="btn" @click="sent">投递</div>
		</u-navbar>
		<view class="form-item">
			<textarea class="input1" v-model="title" placeholder="标题"></textarea>
		  <textarea class="input2" v-model="message" placeholder="在这里,没有人了解你的过去,没有你抒发不了的烦恼,所以请尽情释放自己吧 (/≧▽≦)/"></textarea>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				// 背景颜色
				 background: 
				 {
				 	backgroundColor: '#001f3f',
				 		backgroundSize: 'cover',
				 		backgroundImage: 'linear-gradient(45deg, rgb(159, 219, 196),rgb(139, 219, 186),rgb(35, 187, 154))'
				 	
				},
				title:'',
				message:''
			}
		},
		methods: {
			backtotreepost()
			{
				uni.navigateTo({
					url:"/pages/info/treecave/treepost"
				})
				
			},
			sent()
			{
				if(this.title==''){
					this.$u.toast("发送失败，请输入标题")
					return
				}
				if(this.message==''){
					this.$u.toast("发送失败，请输入内容")
					return
				}
				uni.request({
					url:"http://localhost:8080/ccPost/publish",
					data:
					{
						userId:0,
						title:this.title,
						postContent:this.message,
						topicId:3
					},
					method:"POST",
					success:(res)=>{
						uni.showToast({ title: '发布成功', icon: 'success' });
						this.title = "";
						this.message = "";
						this.backtotreepost();
					}
				})
			},
		}
	}
</script>

<style>
.btnstyle
{
	color: #4d8e4c;
	background-color: #f8fdf8;
}
.btn 
{
    text-align: right;
    padding: 20rpx;
    margin: 0 auto;
	margin-right: 10%;
    width: 9%;
    background-color: #3a8b3f;
	border-radius: 40%;
    color: #ffffff;
	},
.form-item 
{
	margin-bottom: 20px;
	/* 添加一个左右边距，使输入框居中 */
	margin-left: auto;
	margin-right: auto;
	margin-top: 30rpx;
	width: 90%; /* 设置一个固定宽度，使左右边距生效 */
}
.input1
{
  width: 100%;
  height: 50rpx;
  padding: 20rpx;
  border: 1px solid #ccc;//框框
  border-radius: 4px;
  /* 通过设置 margin-left 负值来向左移动输入框 */
  margin-left: -10px;
  margin-bottom: 50rpx;
  background-color: #f8fdf8;
}
.input2
{
  width: 100%;
  height: 360rpx;
  padding: 10px;
  border: 1px solid #ccc;//框框
  border-radius: 4px;
  /* 通过设置 margin-left 负值来向左移动输入框 */
  margin-left: -10px;
  background-color: #f8fdf8;
}
.help-container {
	padding: 20px;
	background-color: #f1fdf1;
   min-height: 100vh; /* 确保至少与视口高度相同 */
}
	.u-demo-wrap {
		background-color: #f1fdf1;
		padding: 40rpx 8rpx;
		margin-left: -14rpx;
		margin-right: -14rpx;
	}
	
	.u-add-wrap {
		flex-direction: column;
		color: $u-content-color;
		font-size: 28rpx;
	}
	
	/deep/ .slot-btn {
		width: 329rpx;
		height: 140rpx;
		display: flex;
		justify-content: center;
		align-items: center;
		background: rgb(244, 245, 246);
		border-radius: 10rpx;
	}

	.slot-btn__hover {
		background-color: rgb(235, 236, 238);
	}

	.pre-box {
		display: flex;
		align-items: center;
		justify-content: space-between;
		flex-wrap: wrap;
	}

	.pre-item {
		flex: 0 0 48.5%;
		border-radius: 10rpx;
		height: 140rpx;
		overflow: hidden;
		position: relative;
		margin-bottom: 20rpx;
	}

	.u-progress {
		position: absolute;
		bottom: 10rpx;
		left: 8rpx;
		right: 8rpx;
		z-index: 9;
		width: auto;
	}

	.pre-item-image {
		width: 100%;
		height: 140rpx;
	}

	.u-delete-icon {
		position: absolute;
		top: 10rpx;
		right: 10rpx;
		z-index: 10;
		background-color: $u-type-error;
		border-radius: 100rpx;
		width: 44rpx;
		height: 44rpx;
		display: flex;
		align-items: center;
		justify-content: center;
	}
</style>
