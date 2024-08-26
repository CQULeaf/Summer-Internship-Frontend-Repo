<template>
	<u-form :model="user">
		<view class="content">
			<view class="title" style="color: #968685;">
				修改密码
			</view>

			<!-- 输入原密码 -->
			<view class="input" style="border: 1px solid #000000;">
				<view class="font">
					原密码
				</view>
				<input type="password" placeholder="输入原密码" v-model="user.oldPassword" />
			</view>

			<!-- 输入新密码 -->
			<view class="input" style="border: 1px solid #000000;">
				<view class="font">
					新密码
				</view>
				<input type="password" placeholder="输入新密码" v-model="user.newPassword1" />
			</view>

			<!-- 再次输入新密码 -->
			<view class="input" style="border: 1px solid #000000;">
				<view class="font">
					确认密码
				</view>
				<input type="password" placeholder="再输入密码" v-model="user.newPassword2" />
			</view>

			<!-- 提交按钮 -->
			<view class="button">
				<view @click="submitForm" class="input">提交</view>
			</view>
		</view>
	</u-form>
</template>

<script>
export default {
  data() {
    return {
      user: {
        oldPassword: '',
        newPassword1: '',
        newPassword2: '',
		username:''
      }
    }
  },
  
  onShow(){
  	const value = uni.getStorageSync('nowAccount');
  	this.user.username=value.data.username
  },
  
  methods: {
    submitForm() {
      if (this.newPassword2 !== this.newPassword1) {
        uni.showToast({
          title: '两次输入的密码不一致',
          icon: 'none'
        });
        return;
      }

      // 发送请求到后端 API
      uni.request({
        url:'http://47.120.1.65:8080/user/updatePassword',
        data:this.user,
        method:"POST",
        header:{
        	'Content-Type': 'application/json'
        },
        success: (res) => {
          console.log(res)
          if (res.data.code == 200) {
            this.$u.toast("修改成功");
			uni.redirectTo({
				url:'/pages/me/setting/setting'
			})
          } else {
            // 处理其他情况，比如错误消息
           this.$u.toast(res.data.data);
          }
        },
        fail: (err) => {
          // 请求失败的处理逻辑
          this.$u.toast("请求失败，请稍后再试");
        }
      });
    }
  }
};

</script>

<style>
	.content { 
		background-color: #fef6f8;
		height: 100vh; /* 修改为100vh，确保覆盖整个视口高度 */
		padding-bottom: 20px; /* 可选，添加一些底部内边距 */
	}

	.title {
		font-size: 8vw;
		text-align: center;
		padding-top: 25vw;
	}

	.input {
	  background-color: #fef6f8; /* 确保与.content背景色一致 */
	  width: 80%;
	  height: 10vw; /* 保持不变 */
	  border-radius: 20vw;
	  margin: 2vw auto;
	  margin-top: 10vw;
	  transition: 1s;
	  position: relative; /* 添加相对定位 */
	}
	
	.input .font {
	  background-color: #fed6dc;
	  width: 30vw;
	  color: #fff;
	  font-size: 3vw;
	  margin-top: -3vw;
	  margin-left: 6vw;
	  text-align: center;
	  border-radius: 20rpx;
	  position: absolute; /* 添加绝对定位 */
	  top: 0; /* 调整字体位置 */
	  left: 0; /* 调整字体位置 */
	}
	
	.input input {
	  background-color: #fef1f4; /* 确保与.content背景色一致 */
	  border: none;
	  margin-top: 2vw; /* 增加margin-top，避免背景色覆盖 */
	  margin-left: 5vw;
	  font-size: 4vw;
	  color: #000000;
	  width: 85%;
	  outline: none;
	  /* 调整input的宽度，以适应背景色 */
	  width: calc(100% - 30vw - 6vw - 20vw);
	}


	/*按钮样式*/
	.button {
		text-align: center;
		margin-top: 100rpx;
	}

	.button .input {
		background-color: #fed6dc;
		border: none;
		padding: 10rpx 0rpx;
		width: 80%;
		height: 80rpx;
		margin: 1 auto;
		border-radius: 20rpx;
		color: #fff;
		font-size: 35rpx;
		line-height: 60rpx;
	}
</style>
