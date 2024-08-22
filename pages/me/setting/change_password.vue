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
				<input type="password" placeholder="输入新密码" v-model="user.newPassword" />
			</view>

			<!-- 再次输入新密码 -->
			<view class="input" style="border: 1px solid #000000;">
				<view class="font">
					确认密码
				</view>
				<input type="password" placeholder="再次输入新密码" v-model="user.confirmPassword" />
			</view>

			<!-- Sign In提交按钮 -->
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
        oldPassword: '1234',
        newPassword: '',
        confirmPassword: ''
      }
    };
  },
  methods: {
    submitForm() {
      if (this.newPassword !== this.confirmPassword) {
        uni.showToast({
          title: '两次输入的密码不一致',
          icon: 'none'
        });
        return;
      }

      // 构建请求数据
      const data = {
        oldPassword: this.oldPassword,
        newPassword: this.newPassword
      };

      // 发送请求到后端 API
      uni.request({
        url:'http://127.0.0.1:4523/m1/5010181-4669608-default/auth/account_settings', // 你的后端 API URL
        method: 'POST',
        data: data,
        header: {
          'content-type': 'application/json' // 根据需要设置请求头
        },
        success: (res) => {
          // 请求成功的处理逻辑
          if (res.statusCode === 200 && res.data.success) {
            uni.showToast({
              title: '密码修改成功',
              icon: 'success'
            });
            // 清空输入框
            this.oldPassword = '';
            this.newPassword = '';
            this.confirmPassword = '';
          } else {
            // 处理其他情况，比如错误消息
            uni.showToast({
              title: res.data.message || '密码修改失败',
              icon: 'none'
            });
          }
        },
        fail: (err) => {
          // 请求失败的处理逻辑
          uni.showToast({
            title: '请求失败，请稍后再试',
            icon: 'none'
          });
        }
      });
    }
  }
};

</script>

<style>
	.content { 
		background-color: #fef1f4;
		height: 100vh; /* 修改为100vh，确保覆盖整个视口高度 */
		padding-bottom: 20px; /* 可选，添加一些底部内边距 */
	}

	.title {
		font-size: 8vw;
		text-align: center;
		padding-top: 25vw;
	}

	.input {
		background-color: #fef1f4; /* 确保与.content背景色一致 */
		width: 80%;
		height: 10vw;
		border-radius: 20vw;
		margin: 2vw auto;
		margin-top: 10vw;
		transition: 1s;
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
	}

	.input input {
		background-color: #fef1f4; /* 确保与.content背景色一致 */
		border: none;
		margin-top: 2vw;
		margin-left: 5vw;
		font-size: 4vw;
		color: #000000;
		width: 85%;
		outline: none;
	}

	/*按钮样式*/
	.button {
		text-align: center;
		margin-top: 50rpx;
	}

	.button .input {
		background-color: #fed6dc;
		border: none;
		padding: 10rpx 0rpx;
		width: 80%;
		height: 80rpx;
		margin: 0 auto;
		border-radius: 20rpx;
		color: #fff;
		font-size: 28rpx;
		line-height: 80rpx;
	}
</style>
