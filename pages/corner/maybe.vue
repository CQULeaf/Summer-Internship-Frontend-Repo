<template>
  <view class="super-topic-detail">
    <text>{{ topic.name }}</text>
    <text>{{topic.description }}</text>
    <!-- ...其他详情信息 -->
  </view>
</template>

<script>
export default {
  data() {
    return {
      Topic: {}
    };
  },
  onLoad(options) {
    // 从页面参数中获取超话ID
    const { topicid } = options;
    this.fetchSuperTopicDetail(topicid);
  },
  methods: {
    fetchSuperTopicDetail(topicid) {
      uni.request({
        url: `http://127.0.0.1:4523/m1/5010181-4669608-default/corner/concerntopost/?topic_id`,
        method: 'GET',
        success: (res) => {
          if (res.statusCode === 200) {
            this.superTopic = res.data;
          } else {
            // 处理错误情况
          }
        },
        fail: (err) => {
          // 处理请求失败
        }
      });
    }
  }
};
</script>