<template>
	<view>
		<view class="cu-card case" :class="isCard?'no-card':''" v-for="(item,index) in like_list" :key="index">
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
						</view>
					</view>
					<view class="text-content" style="word-break: break-all;">
						{{item.describe}}
					</view>
					<view class=" flex-sub padding-lr">
						<swiper indicator-dots="true" circular="true" class="swiper">
							<swiper-item v-for="image in item.img" :key="index1">
								<image v-bind:src="image" style="height: 100%;" @tap="viewimage" :data-url="image">
								</image>
							</swiper-item>
						</swiper>
					</view>
					<view class="text-gray text-sm text-right padding">
						<view  @tap="Dian_Zan" :data-index="index" class="image_view">
							<image  src="../../static/like_checked.png" class="like_image"></image>
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
				like_list: [],
				image: [],
				id: "",
			}
		},
		created() {
			this.like_list = getApp().globalData.like_list
			console.log(this.like_list)
		},
		methods: {
			viewimage(e) { //浏览图片
				this.image.push(e.currentTarget.dataset.url) //将图片路径放入数组
				wx.previewImage({
					urls: this.image,
					current: e.currentTarget.dataset.url
				}).then(this.image.pop()) //浏览完图片后将数组数据弹出
			},
			Dian_Zan(e) {
					wx.showModal({
						title:'是否取消点赞',
						cancelText:'取消',
						confirmText:'确定',
						success:res=>{
							if(res.confirm){
								var x = this.like_list[e.currentTarget.dataset.index].like_number - 1
								var y = this.like_list[e.currentTarget.dataset.index].like_user
								for(var i=0 ;i< y.length;i++){
									if(y[i] == getApp().globalData.avatarUrl){
										y.splice(i,1)
										break
									}
								}
								db.collection(this.like_list[e.currentTarget.dataset.index].type).where({
									_id:this.like_list[e.currentTarget.dataset.index]._id
								}).update({
									data:{
										like_number:x,
										like_user:y,
									}
								})
							}
						}
					})
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
