<template>
    <view class="settings">
      <view class="settings-header">
        <text class="header-text">设置</text>
      </view>
      <view class="item" @click="goToAccountSettings">
        <text class="item-text">修改密码</text>
      </view>
      
      <view class="item"@click="goToInfor">
        <text class="item-text">个人信息</text>
      </view>
	  <view class="item" @click="goToHelp">
	    <text class="item-text">帮助与客服</text>
	  </view>
	  
	  <view class="item"@click="goTologin">
	    <text class="item-text">退出登录</text>
	  </view>
    </view>
  </template>
  
  <script>
  export default {
    methods: {
      goToAccountSettings() {
        uni.navigateTo({
          url: '/pages/me/setting/change_password'
        });
		
      },
	  goTologin() {
	    uni.navigateTo({
	      url: '/pages/me/setting/login'
	    });
	  		
	  },
	  goToHelp() {
	    uni.navigateTo({
	      url: '/pages/me/setting/help'
	    });
	  		
	  },
	  goToInfor() {
	    uni.navigateTo({
	      url: '/pages/me/myinfo/Information'
	    });
	  		
	  }
	  
    }
  };
  </script>
  
  <style>
    .settings {
      background-color: #fef1f4;
      padding: 20px;
      border-radius: 8px;
      min-height: 100vh; /* 确保至少与视口高度相同 */
    }
    
    .settings-header {
      margin-bottom: 30px;
      display: flex;
    }
    
    .header-text {
      font-size: 20px;
      font-weight: bold;
      margin-left: 10px;
      color: #fed6dc;
    }
    
    .item {
      display: flex;
      align-items: center;
      justify-content: space-between;
      margin-bottom: 20px;
      padding: 12px 16px;
      border-radius: 6px;
      background-color: #ffffff;
    }
    
    .item-text {
      flex: 1;
      font-size: 18px;
      margin-left: 12px;
      color: #333333;
    }
    
    .item:active {
      background-color: #ffffff;
    }
  </style>
