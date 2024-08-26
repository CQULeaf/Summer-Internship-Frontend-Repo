<template>
	<view>
		<u-navbar class="wrap" height=60 title="我的帖子" title-size=40 :background="background" is-back=true
			:customBack="gotopofile">
		</u-navbar>
    <view v-if="post">
      <text class="post-title">{{ post.title }}</text>
      <text class="post-content">{{ post.content }}</text>
      <!-- 其他帖子详细信息 -->
    </view>
    <view v-else>
      <text>加载中...</text>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      post: null,
	  background: {
	  	backgroundColor: '#001f3f',
	  	backgroundSize: 'cover',
	  	backgroundImage: 'linear-gradient(45deg, rgb(255, 217, 218),rgb(255, 184, 191))'
	  },
    };
  },
  onLoad(options) {
    const postId = options.postId;
    this.fetchPostDetails(postId);
  },
  methods: {
	  gotopofile() {
	  	uni.switchTab({
	  		url: "/pages/me/mypage"
	  	})
	  },
    fetchPostDetails(postId) {
      uni.request({
        url: `http://127.0.0.1:4523/m1/5010181-4669608-default/ccPost/getPost/${postId}`,
        method: 'GET',
        success: (res) => {
          if (res.statusCode === 200) {
            this.post = res.data.data;
          } else {
            uni.showToast({ title: '获取帖子详情失败', icon: 'none' });
          }
        },
        fail: (err) => {
          uni.showToast({ title: '请求失败', icon: 'none' });
        }
      });
    }
  }
};
</script>

<style lang="scss">
.post-title {
  font-size: 24px;
  font-weight: bold;
}
.post-content {
  font-size: 16px;
  margin-top: 10px;
}
</style>
