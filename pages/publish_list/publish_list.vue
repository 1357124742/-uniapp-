<template>
	<view>
		<view class="cu-card case" :class="isCard?'no-card':''" v-for="(item,index) in publish_list" :key="index">
			<view class="cu-card dynamic" :class="isCard?'no-card':''">
				<view class="cu-item shadow">
					<view class="cu-list menu-avatar">
						<view class="cu-item">
							<view class="cu-avatar round lg">
								<image v-bind:src="item.avatarUrl" mode="widthFix" style="border-radius: 50%;"></image>
							</view>
							<view class="content flex-sub">
								<view>
									{{item.nickName}}
								</view>
								<view class="text-gray text-sm flex justify-between">
									{{item.show1}}&ensp;&ensp;&ensp;{{item.show2}}&ensp;&ensp;&ensp;&ensp;{{item.show3}}
								</view>
							</view>
							<view class="tiezi" @tap="delete_tiezi" :data-index="index">
								<image class="delete" src="../../static/删除分类.png"
									:data-index="index"></image>
							</view>
						</view>
					</view>
					<view class="text-content" style="word-break: break-all;">
						{{item.describe}}
					</view>
					<view class=" flex-sub padding-lr">
						<swiper indicator-dots="true" circular="true" class="swiper">
							<swiper-item v-for="image in item.img" :key="index1">
								<image v-bind:src="image" style="height: 100%;" @tap="viewimage" :data-url="image" :data-index="index">
								</image>
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
				publish_list: [],
				image: [],
				id: "",
				checked :[],
			}
		},
		created() {
			this.publish_list = getApp().globalData.publish_list
			console.log(this.publish_list)
			var times = 0
			for(var i = 0; i<this.publish_list.length; i++)
			{
				if(this.publish_list[i].like_user.length != 0){
					for(var j =0 ;j < this.publish_list[i].like_user.length; j++){
						if(this.publish_list[i].like_user[j] == getApp().globalData.avatarUrl){
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
		methods: {
			viewimage(e) { //浏览图片
				this.image.push(e.currentTarget.dataset.url) //将图片路径放入数组
				wx.previewImage({
					urls: this.image,
					current: e.currentTarget.dataset.url
				}).then(this.image.pop()) //浏览完图片后将数组数据弹出
			},
			delete_tiezi(e) {
				this.id = getApp().globalData.publish_list[e.currentTarget.dataset.index]._id
				// console.log(getApp().globalData.publish_list[e.currentTarget.dataset.index].img[0])
				wx.showModal({
					title: "是否确定删除",
					success: res => {
						if (res.confirm) {
								db.collection(this.publish_list[e.currentTarget.dataset.index].type).doc(this.id).remove().then(console.log("已经成功删除"))
						}
					}
				})
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
					db.collection(this.publish_list[e.currentTarget.dataset.index].type).where({
							like_user: getApp().globalData.avatarUrl,
						})
						.get({
							success: res => { //res返回了全部数据集合
								for (var i = 0; i < res.data.length; i++) { //循环了res的总数次
									if (this.publish_list[e.currentTarget.dataset.index]._id == res.data[i]._id) { //如果点赞的帖子已经被点赞过了
										var y = this.publish_list[e.currentTarget.dataset.index].like_user //将该帖子的点赞用户表传给y
										for (var j = 0; j < y.length; j++) { //查找该用户位于点赞数组的位置
											if (y[j] == getApp().globalData.avatarUrl) {
												y.splice(j, 1)
												break
											}
										}
										var x = this.publish_list[e.currentTarget.dataset.index].like_number - 1
										wx.showModal({
											title: '您点赞过该帖子,是否取消点赞',
											confirmText: '确定取消',
											cancelText: '不取消',
											success: res => {
												if (res.confirm) {
													db.collection("love").doc(this.publish_list[e.currentTarget.dataset.index]._id).update({
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
								var x = this.publish_list[e.currentTarget.dataset.index].like_number + 1
								var y = this.publish_list[e.currentTarget.dataset.index].like_user //将该帖子的点赞用户表传给y
								y.push(getApp().globalData.avatarUrl)
								wx.showModal({
									title: '确定点赞该帖子吗?',
									confirmText: '确定点赞',
									cancelText: '点错了',
									success: res => {
										if (res.confirm) {
											db.collection("love").doc(this.publish_list[e.currentTarget.dataset.index]._id).update({
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
	.swiper {
		width: 100%;
		height: 500rpx;
	}

	.tiezi {
		width: 70rpx;
		height: 70rpx;
		float: right;
		margin-right: 30rpx;
	}

	.delete {
		height: 40rpx;
		width: 40rpx;
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
