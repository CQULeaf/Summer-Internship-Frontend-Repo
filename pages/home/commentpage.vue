<template>
  <view class="help-container">
	<view>
		<u-navbar :is-back="true" title="评论" :background="background" :customBack="backtohome" height="55" title-size=40>
		</u-navbar>
	</view>
    <form @submit.prevent="submitForm">
      <view class="form-item">

        <textarea class="input" v-model="message" placeholder="留下你的想法吧..."></textarea>
      </view>
      <button type="submit" class="sent-button">发送</button>
    </form>
  </view>
</template>

<script>
export default {
  data() {
    return {
      background:
       {
       	backgroundColor:'#fed6dc'
      },
    };
  },
  methods: {
    submitForm() {
      // 这里可以添加提交请求的逻辑，例如发送请求到后端
      uni.showToast({
        title: '请求已提交',
        icon: 'success'
      });
      // 清空输入框
      this.message = '';
    },
	backtohome()
	{
		uni.switchTab({
			url:"/pages/home/homepage"
		})
		
	}
  }
};
</script>

<style>
.help-container {
  padding: 20px;
    
}

.form-item {
  margin-bottom: 30px;
  margin-top: 15px;
  /* 添加一个左右边距，使输入框居中 */
  margin-left: auto;
  margin-right: auto;
  width: 90%; /* 设置一个固定宽度，使左右边距生效 */
  
}


.input {
  width: 100%;
  height: 150px;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  /* 通过设置 margin-left 负值来向左移动输入框 */
  margin-left: -10px;
}

.sent-button {
  width: 95%;
  height: 45px;
  background-color: #fed1da;
  color: white;
  border: #feb2c3;
  border-radius: 10px;
  margin-top: 10%;
}
</style>