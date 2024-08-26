<template>
	<view>
		<view>
			<view class="slot-wrap">
				<u-tabs-swiper ref="uTabs" :list="pagelist" :current="pagecurrent" @change="tabsChange"
					:is-scroll="false" :bold="false" bg-color="#ffc7cb" active-color="#000000"></u-tabs-swiper>
			</view>
			<swiper class="swiper" :current="swiperCurrent">
				<swiper-item v-for="(tab, tabindex) in pagelist" :key="tabindex">
					<scroll-view class="scroll-view" scroll-y>
						<view class="list">
							<view v-if="pagelist[pagecurrent].type === 'recommend'">
								<view class="list-item" v-for="(item, index) in currentItems" :key="index" @click="goToContent(item.topic_id)">
									<!-- 这里可以显示item的相关属性 -->
									<view>{{ item.cover }}</view>
									<view>{{ item.description }}</view>
								</view>
							</view>
							<view v-if="pagelist[pagecurrent].type === 'like'">
								<view class="list-item" v-for="(item, index) in currentItems" :key="index" @click="goToContent(item.topic_id)">
									<!-- 这里可以显示item的相关属性 -->
									<view>{{ item.cover }}</view>
									<view>{{ item.description }}</view>
								</view>
							</view>
						</view>
					</scroll-view>
				</swiper-item>
			</swiper>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list: '',
				current: 4,
				pagelist: [
					{
						type: 'like',
						api: 'http://localhost:8080/corner/superWordNameRecommend'
					},
					{
						name: '推荐',
						type: 'recommend',
						api: 'http://localhost:8080/corner/superWordNameRecommend',
						url: "http://localhost:8080/corner/getTopicsByFlagAndUser",
					}
				],
				pagecurrent: 0,
				swiperCurrent: 0,
				matchuser2: {
					name: '', cover: '', description: '', post_count: '', follower_count: '
