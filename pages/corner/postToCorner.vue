<template>
	<view class="help-container">
		<u-navbar :is-back="true" title="发帖子" :background="background" :customBack="backtocontent" height="55">
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
					<u-upload @on-choose-fail="onChooseFail" :before-remove="beforeRemove" ref="uUpload" :custom-btn="customBtn" :show-upload-list="showUploadList" :action="action" 
					 :show-progress="showProgress" :deletable="deletable" :max-count="maxCount" @on-list-change="onListChange" max-count=1>
						<view v-if="customBtn" slot="addBtn" class="slot-btn" hover-class="slot-btn__hover" hover-stay-time="150">
							<u-icon name="photo" size="60" :color="$u.color['lightColor']"></u-icon>
						</view>
					</u-upload>
					<u-button :custom-style="{marginTop: '20rpx'}" @click="upload">上传</u-button>
					<u-button :custom-style="{marginTop: '40rpx'}" @click="clear">清空列表</u-button>
				</view>
			</view>
		</view>
		
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
				 	backgroundColor:'#abecff'
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
					topicId: 0
				}
			}
		},
		
		methods: {
			backtocontent()
			{
				uni.navigateTo({
					url:"/pages/corner/content"
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
						uni.getStorage({
							key:"matchuser2",
							success:(res2)=>{
								console.log(res2.data.topicId)
								this.addPost.userId=res.data.data.userId
								this.addPost.title=this.title
								this.addPost.postContent=this.message
								this.addPost.topicId=res2.data.topicId
							}
						})
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
				uni.navigateTo({
					url:"/pages/corner/content"
				})
			},
			
			clear() {
				this.$refs.uUpload.clear();
			},
			controlChange(index) {
				if(index == 0) {
					this.showProgress = true;
					this.deletable = true;
				} else {
					this.showProgress = false;
					this.deletable = false;
				}
			},
			maxCountChange(index) {
				this.maxCount = index == 0 ? 1 : index == 1 ? 2 : 4;
			},
			customStyleChange(index) {
				if (index == 0) {
					this.showUploadList = false;
					this.customBtn = true;
					
				} else {
					this.showUploadList = true;
					this.customBtn = false;
				}
			},
			upload() {
				let files = [];
				files = this.$refs.uUpload.lists;
				console.log(files[0])
				if(files[0].response.code===400){
					this.$u.toast("文件太大，无法上传")
					return
				}
				if(files[0].response==null){
					this.$u.toast("上传失败，请重试")
					return
				}else{
					this.$u.toast("上传成功")
				}
				this.addPost.cover=files[0].response
			},
			deleteItem(index) {
				this.$refs.uUpload.remove(index);
			},
			onListChange(lists) {
				// console.log('onListChange', lists);
				this.lists = lists;
			},
			beforeRemove(index, lists) {
				return true;
			},
			onChooseFail(e) {
				onsole.log(e);
			}
		}
	}
</script>

<style>
.btn 
{
    text-align: right;
    padding: 8px;
    margin: 0 auto;
	margin-right: 10%;
    width: 9%;
    background-color: #509cff;
	border-radius: 40%;
    color: #ffffff;
	},

.form-item
{
	margin-bottom: 15px;
	/* 添加一个左右边距，使输入框居中 */
	margin-left: auto;
	margin-right: auto;
	margin-top: 10px;
	width: 90%; /* 设置一个固定宽度，使左右边距生效 */
	background-color: #fff;
}
.input1
{
  width: 100%;
  height: 25px;
  padding: 10px;
  border: 1px solid #ccc;//框框
  border-radius: 4px;
  /* 通过设置 margin-left 负值来向左移动输入框 */
  margin-left: -10px;
}
.input2 
{
  width: 100%;
  height: 150px;
  padding: 10px;
  border: 1px solid #ccc;//框框
  border-radius: 4px;
  /* 通过设置 margin-left 负值来向左移动输入框 */
  margin-left: -10px;
}
.help-container {
	padding: 20px;
	background-color: #fdfdfd;
   min-height: 100vh; /* 确保至少与视口高度相同 */
   background-color: #f4fdff;
}
	.u-demo-wrap {
		background-color: #fdfdfd;
		padding: 40rpx 8rpx;
		margin-left: -14rpx;
		margin-right: -14rpx;
		background-color: #f4fdff;
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
