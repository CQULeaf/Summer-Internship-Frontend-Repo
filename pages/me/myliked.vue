
<!-- 假设现在已经把评论移花接木了 -->
<template>
  <view class="post-container">
    <view class="post-header">
      <image class="avatar" :src="user.avatar" mode="aspectFill"></image>
      <text class="user-id">{{ user.id }}</text>
    </view>
    <view class="post-content">
      {{ postContent }}
    </view>
    <view class="post-actions">
      <button class="like-btn" @click="handleLike">
        <uni-icons type="heart" size="10" :color="isLiked ? '#ff6c60' : '#ccc'"></uni-icons>
        <text class="action-text">点赞</text>
      </button>
      <button class="comment-btn" @click="handleComment">
        <uni-icons type="chat" size="10" color="#2196f3"></uni-icons>
        <text class="action-text">评论</text>
      </button>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      user: {
        avatar: "/static/happierp.png", // 发布者头像URL
        id: 'user123', // 发布者ID
      },
      postContent: '元和十年，予左迁九江郡司马。明年秋，送客湓浦口，闻舟中夜弹琵琶者，听其音，铮铮然有京都声。问其人，本长安倡女，尝学琵琶于穆、曹二善才，年长色衰，委身为贾人妇。遂命酒，使快弹数曲。曲罢悯然，自叙少小时欢乐事，今漂沦憔悴，转徙于江湖间。予出官二年，恬然自安，感斯人言，是夕始觉有迁谪意。因为长句，歌以赠之，凡六百一十六言，命曰《琵琶行并序》。', // 帖子内容
      isLiked: false, // 是否已点赞
    };
  },
  methods: {
    handleLike() {
      // 点赞逻辑
      this.isLiked = !this.isLiked;
      // 更新点赞数量或者状态
      console.log('点赞');
    },
    handleComment() {
      // 评论逻辑
      console.log('评论');
	  //跳转试试？
	  uni.navigateTo({
	          url: '/pages/home/reply',
	        })
	  
    },
  },
};
</script>

<style>
.post-container {
  display: flex;
  flex-direction: column;
  padding: 10px;
  border-bottom: 1px solid #ccc;
}

.post-header {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
}

.avatar {
  width: 50px;
  height: 50px;
  border-radius: 50%;
  margin-right: 10px;
}

.user-id {
  font-size: 16px;
  font-weight: bold;
}

.post-content {
  font-size: 14px;
}

.post-actions {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 10px;
}

.like-btn,
.comment-btn {
  background-color: #f5f5f5;
  border: none;
  padding: 10px;
  font-size: 14px;
}

.action-text {
  font-size: 14px;
  margin-left: 5px;
  margin-right: 5px;
}
</style>
