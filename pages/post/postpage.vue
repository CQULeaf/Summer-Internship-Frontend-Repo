<template>
	<view class="help-container">
		<u-navbar :is-back="true" title="发帖子" :background="background" :customBack="backtohome" height="55">
			<div class="btn" @click="sendPost()">发送</div>
		</u-navbar>
		<view class="form-item">
		  <textarea class="input1" v-model="title" placeholder="标题"></textarea>
		</view>
		<view class="form-item">
		  <textarea class="input2" v-model="message" placeholder="分享新鲜事..."></textarea>
		</view>
		<view class="u-demo">
			<view class="u-demo-wrap">
				<view class="u-demo-area">
					<u-toast ref="uToast"></u-toast>
					<view class="pre-box" v-if="!showUploadList">
						<view class="pre-item" v-for="(item, index) in lists" :key="index">
							<image class="pre-item-image" :src="item.url" mode="aspectFill"></image>
							<view class="u-delete-icon" @tap.stop="deleteItem(index)">
								<u-icon name="close" size="20" color="#ffffff"></u-icon>
							</view>
							<u-line-progress v-if="item.progress > 0 && !item.error" :show-percent="false" height="16" class="u-progress"
							 :percent="item.progress"></u-line-progress>
						</view>
					</view>
					
					<!-- 图片选择 -->
					<u-upload @on-choose-fail="onChooseFail" :before-remove="beforeRemove" ref="uUpload" :custom-btn="customBtn" :show-upload-list="showUploadList" :action="action"
					 :show-progress="showProgress" :deletable="deletable" :max-count="maxCount" @on-list-change="onListChange" max-count=1>
						<view v-if="customBtn" slot="addBtn" class="slot-btn" hover-class="slot-btn__hover" hover-stay-time="150">
							<u-icon name="photo" size="60" :color="$u.color['lightColor']"></u-icon>
						</view>
					</u-upload>
					<u-button :custom-style="{marginTop: '20rpx'}" @click="upload">上传</u-button>
					<!-- <u-button :custom-style="{marginTop: '40rpx'}" @click="clear">清空列表</u-button> -->
				</view>
			</view>
		</view>
		<u-tabbar v-model="current" :list="list" :mid-button="true"></u-tabbar>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				action: 'http://localhost:8080/upload',
				// 背景颜色
				 background: 
				 {
				 	backgroundColor:'#fed6dc'
				},
				message:'',
				title:"",
				showUploadList: true,
				customBtn: false,
				autoUpload: false,
				showProgress: true,
				deletable: true,
				customStyle: false,
				maxCount: 2,
				lists: [], // 组件内部的文件列表
				list:'',
				current: 4,
				title:"",
				message:"",
				
				addPost:{
					userId: 1,
					title: "",
					postContent: "",
					commentCount: 0,
					likeCount: 0,
					createdAt: "",
					updatedTime: "",
					updatedAt: "",
					deletedAt: "",
					cover: null,
					topicId: 13
				}
			}
		},
		
		 onLoad: function (options) {
		  setTimeout(function () {
		   console.log('start pulldown');
		  }, 1000);
		  uni.startPullDownRefresh();
		 },
		  onPullDownRefresh() {
		  console.log('refresh');
		  setTimeout(function () {
		   uni.stopPullDownRefresh();
		  }, 1000);
		 },
		 
		onReady() {
			// 得到整个组件对象，内部图片列表变量为"lists"
			this.lists = this.$refs.uUpload.lists;
		},
		
		onLoad() {
			this.list = [{
					iconPath: "/static/newhomeg.png",
					selectedIconPath: "/static/newhomep.png",
					text: '家',
					
					customIcon: false,
					pagePath:'/pages/home/homepage'
				},
				{
					iconPath:  "/static/happygrey.png",
					selectedIconPath:"/static/happierp.png",
					text: '聚',
					
					customIcon: false,
					pagePath:'/pages/corner/corner'
				},
				{
					iconPath: "/static/yanblack.png",
					selectedIconPath: "/static/yanpink.png",
					text: '言',
					midButton: true,
					customIcon: false,
					pagePath:'/pages/post/postpage'
				},
				{
					iconPath:  "/static/messagegrey.png",
					selectedIconPath:"/static/messagep.png",
					text: '讯',
					customIcon: false,
					pagePath:'/pages/info/infopage'
				},
				{
					iconPath:  "/static/megrey.png",
					selectedIconPath:"/static/mep.png",
					text: '我',
					isDot: false,
					customIcon: false,
					pagePath:'/pages/me/mypage'
				},
			]
		},
		methods: {
			submit() {
				let files = [];
				// 如果您不需要进行太多的处理，直接如下即可
				files = this.$refs.uUpload.lists;
				console.log(files)
			},
			
			backtohome()
			{
				uni.switchTab({
					url:"/pages/home/homepage"
				})
				
			},
			
			sendPost() {
				if(this.title==''){
					this.$u.toast("发送失败，请输入标题")
					return
				}
				if(this.message==''){
					this.$u.toast("发送失败，请输入内容")
					return
				}
				uni.getStorage({
					key:"nowAccount",
					success:(res)=>{
						this.addPost.userId=res.data.data.userId
						this.addPost.title=this.title
						this.addPost.postContent=this.message
						this.addPost.topicld=0
					}
				})
		
				// 发起请求
				uni.request({
					url: 'http://localhost:8080/ccPost/publish',
					data:this.addPost,
					method: 'POST',
					header: { 'Content-Type': 'application/json' },
					success: (res) => {
						console.log('发布成功', res.data);
						uni.showToast({ title: '发布成功', icon: 'success' });
						// 清空输入框内容
						this.title = "";
						this.message = "";
						this.backtohome(); // 返回首页
					},
					fail: (err) => {
						console.error('发布失败', err);
						uni.showToast({ title: '发布失败', icon: 'none' });
					}
				});
				uni.switchTab({
					url:"/pages/home/homepage"
				})
			},
						
			//发送图片
			clear() {
				this.$refs.uUpload.clear();
			},
			upload() {
				let files = [];
				// 如果您不需要进行太多的处理，直接如下即可
				files = this.$refs.uUpload.lists;
				console.log(files[0].response)
				this.addPost.cover=files[0].response
			},
			deleteItem(index) {
				this.$refs.uUpload.remove(index);
			},
			onListChange(lists) {
				this.lists = lists;
			},
			beforeRemove(index, lists) {
				return true;
			},
			onChooseFail(e) {
				console.log(e);
			}
		}
	}
</script>

<style>
.btn 
{
    text-align: right;
    padding: 20rpx;
    margin: 0 auto;
	margin-right: 10%;
    width: 9%;
    background-color: #66b1ff;
	border-radius: 40%;
    color: #ffffff;
	}

.form-item
{
	margin-bottom: 25px;
	/* 添加一个左右边距，使输入框居中 */
	margin-left: auto;
	margin-right: auto;
	margin-top: 10px;
	width: 90%; /* 设置一个固定宽度，使左右边距生效 */
	background-color: #ffffff;
}
.input1
{
  width: 100%;
  height: 40rpx;
  padding: 20rpx;
  border: 1px solid #ccc;
  border-radius: 4px;
  /* 通过设置 margin-left 负值来向左移动输入框 */
  margin-left: -10px;
}
.input2 
{
  width: 100%;
  height: 350rpx;
  padding: 20rpx;
  border: 1px solid #ccc;
  border-radius: 4px;
  /* 通过设置 margin-left 负值来向左移动输入框 */
  margin-left: -10px;
}

.help-container {
	padding: 20px;
	background-color: #fdfdfd;
   min-height: 100vh; /* 确保至少与视口高度相同 */
}
	.u-demo-wrap {
		background-color: #fdfdfd;
		padding: 40rpx 8rpx;
		margin-left: -14rpx;
		margin-right: -14rpx;
	}
	
	.u-add-wrap {
		flex-direction: column;
		color: u-content-color;
		font-size: 28rpx;
	}
	
  .slot-btn {
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
		background-color: u-type-error;
		border-radius: 100rpx;
		width: 44rpx;
		height: 44rpx;
		display: flex;
		align-items: center;
		justify-content: center;
	}
</style>