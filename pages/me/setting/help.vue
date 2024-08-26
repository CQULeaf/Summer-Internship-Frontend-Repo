<template>
  <view class="help-container">
    <form @submit.prevent="submitForm">
      <view class="form-item">
        <text class="label">问题或请求</text>
        <textarea class="input" v-model="message" placeholder="请输入您的问题或请求"></textarea>
      </view>
      <button type="submit" class="submit-button">提交</button>
    </form>
  </view>
</template>

<script>
export default {
  data() {
    return {
      message: ''//变量
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
    }
  }
};
</script>

<style>
.help-container {
  padding: 20px;
    width: 100%;
	
}

.form-item {
  margin-bottom: 20px;
  /* 添加一个左右边距，使输入框居中 */
  margin-left: auto;
  margin-right: auto;
  width: 90%; /* 设置一个固定宽度，使左右边距生效 */
}

.label {
  display: block;
  margin-bottom: 10px;
  font-size: 16px;
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

.submit-button {
  width: 95%;
  height: 40px;
  background-color: #ffd7dd;
  color: white;
  border: none;
  border-radius: 4px;
}
</style>