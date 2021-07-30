<template>
	<view>
		<view>
			<view class="round-click bg-gradual-blue position" @tap="publish">
				发布
			</view>
		</view>
		<view class="cu-card case" :class="isCard?'no-card':''" v-for="(item,index) in Search" :key="index">
			<view class="cu-card dynamic" :class="isCard?'no-card':''">
				<view class="cu-item shadow background">
					<view class="cu-list menu-avatar">
						<view class="cu-item">
							<view class="cu-avatar round lg">
								<image v-bind:src="item.avatarUrl" mode="widthFix" style="border-radius: 50%;"></image>
							</view>
							<view class="content flex-sub">
								<view>{{item.nickName}}</view>
								<view class="text-gray text-sm flex justify-between">
									{{item.show1}}&emsp;&emsp;&emsp;{{item.show2}}&emsp;&emsp;{{item.show3}}
								</view>
							</view>
						</view>
					</view>
					<view class="text-content margin-top" style="word-break: break-all;">
						{{item.describe}}
					</view>
					<view class=" flex-sub padding-lr">
						<swiper indicator-dots="true" circular="true" class="swiper">
							<swiper-item v-for="image in item.img" :key="index1">
								<image v-bind:src="image" style="width: 100%;" @tap="viewimage" :data-url="image"></image>
							</swiper-item>
						</swiper>
					</view>
					<view class="text-gray text-sm text-right padding">
						<view v-if="checked[index] == 1"  @tap="Dian_Zan" :data-index="index" class="image_view">
							<image  src="../../static/like_checked.png" class="like_image"></image>
							<view class="image_title">{{item.like_number}}</view>
						</view>
						<view v-else @tap="Dian_Zan" :data-index="index"  class="image_view">
							<image src="../../static/like.png" class="like_image"></image>
							<view class="image_title">{{item.like_number}}</view>
						</view>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	const db = wx.cloud.database()
	export default {
		data() {
			return {
				Search: [],
				image: [],
				checked :[],
			};
		},
		created() {
			db.collection("search").get({
				success: res => {
					this.Search = res.data
					this.Search.reverse()
				},

			})
		},
		onPullDownRefresh() {
			db.collection("search").get({
				success: res => {
					this.Search = res.data
					this.Search.reverse()
					var times = 0
					for(var i = 0; i<this.Search.length; i++)
					{
						if(this.Search[i].like_user.length != 0){
							for(var j =0 ;j < this.Search[i].like_user.length; j++){
								if(this.Search[i].like_user[j] == getApp().globalData.avatarUrl){
									this.checked[times] = 1
									break
								}
								this.checked[times] = 0
							}
						}else{
							this.checked[times] = 0
						}
						times = times + 1
					}
				},

			})
		},
		methods: {
			viewimage(e) {
				this.image.push(e.currentTarget.dataset.url)
				wx.previewImage({
					urls: this.image,
					current: e.currentTarget.dataset.url
				}).then(this.image.pop())
			},
			publish(e) {
				if(getApp().globalData.nickName!='点击登录'){
					wx.navigateTo({
						url:"../publish/publish"
					})
				}else{
					wx.showModal({
						title:'请先登录',
						showCancel:false,
						success:res=>{
							if(res.confirm){
								wx.switchTab({
									url:"../my/my"
								})
							}
						}
					})
				}
			},
			Dian_Zan(e) {
				if (getApp().globalData.avatarUrl == '../../static/icon/avatar.png') {
					wx.showModal({
						title: '请先登录',
						showCancel: false,
						success: res => {
							if (res.confirm) {
								wx.switchTab({
									url: "../my/my"
								})
							}
						}
					})
				} else {
					db.collection("search").where({
							like_user: getApp().globalData.avatarUrl,
						})
						.get({
							success: res => { //res返回了全部数据集合
								for (var i = 0; i < res.data.length; i++) { //循环了res的总数次
									if (this.Search[e.currentTarget.dataset.index]._id == res.data[i]._id) { //如果点赞的帖子已经被点赞过了
										var y = this.Search[e.currentTarget.dataset.index].like_user //将该帖子的点赞用户表传给y
										for (var j = 0; j < y.length; j++) { //查找该用户位于点赞数组的位置
											if (y[j] == getApp().globalData.avatarUrl) {
												y.splice(j, 1)
												break
											}
										}
										var x = this.Search[e.currentTarget.dataset.index].like_number - 1
										wx.showModal({
											title: '您点赞过该帖子,是否取消点赞',
											confirmText: '确定取消',
											cancelText: '不取消',
											success: res => {
												if (res.confirm) {
													db.collection("search").doc(this.Search[e.currentTarget.dataset.index]._id).update({
														data: {
															like_number: x,
															like_user: y,
														}
													})
												}
											}
										})
										return;
									}
								}
								var x = this.Search[e.currentTarget.dataset.index].like_number + 1
								var y = this.Search[e.currentTarget.dataset.index].like_user //将该帖子的点赞用户表传给y
								y.push(getApp().globalData.avatarUrl)
								wx.showModal({
									title: '确定点赞该帖子吗?',
									confirmText: '确定点赞',
									cancelText: '点错了',
									success: res => {
										if (res.confirm) {
											db.collection("search").doc(this.Search[e.currentTarget.dataset.index]._id).update({
												data:{
													like_number: x,
													like_user: y,
												}
											})
										}
									}
								})
							},
						})
				}
			},
		}
	}
</script>

<style>
	.background{
		background-color: #FFFFFF;
	}
	.position {
		margin-right: 50rpx;
		width: 90rpx;
		height: 90rpx;
	}
	
	.swiper {
		width: 100%;
		height: 500rpx;
		margin: 0 auto;
	}
	
	.image_view{
		margin-left: 550rpx;
		width: 80rpx;
		height: 40rpx;
		text-align: right;
	}
	.like_image{
		width: 40rpx;
		height: 40rpx;
	}
	.image_title{
		margin-top: 10rpx;
		width :20rpx;
		height: 20rpx;
		float: right;
	}
</style>
