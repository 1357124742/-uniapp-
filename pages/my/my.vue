<template>
	<view>
		<view class="meMain">
			<image src="../../static/icon/meBg.png"></image>
			<view class="mePosition">
				<view class="meMainBox">
					<view class="meHead" @click="BindGetUserInfo()">
						<view class="meHeadAvatar">
							<image :src="avatarUrl" mode="aspectFill"></image>
						</view>
						<view class="meHeadName">
							<text>{{ nickName }}</text>
						</view>
					</view>
					<view class="meOverBg">
						<view class="meVisitor">
							<view class="meVisitorLt" @tap="Publish_list">
								<view class="meVisitorTxt_02">{{my_publish}}</view>
								<view class="meVisitorTxt_01">我的发布</view>
							</view>
							<view class="meVisitorLt" @tap="Like_list">
								<view class="meVisitorTxt_02">{{my_like}}</view>
								<view class="meVisitorTxt_01">我的点赞</view>
							</view>
						</view>
					</view>
					<view class="meOverBg">
						<view class="businessList" @tap="about">
							<view class="businessListTxt">
								<image src="../../static/icon/meIcon_01.png"></image>关于程序
							</view>
							<view class="businessListYou">
								<image src="../../static/icon/you.png"></image>
							</view>
						</view>
						<view class="businessList" @tap="connect">
							<view class="businessListTxt">
								<image src="../../static/icon/meIcon_01.png"></image>联系我们
							</view>
							<view class="businessListYou">
								<image src="../../static/icon/you.png"></image>
							</view>
						</view>
						<button open-type="contact" class="cu-btn block bg-blue margin-tb-sm lg">联系客服</button>
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
				avatarUrl: getApp().globalData.avatarUrl,
				nickName: getApp().globalData.nickName,
				token: getApp().globalData.token,
				my_publish: 0,
				my_like: 0,
				publish_list: [],
				like_list: [],
				x: 0,
				y: 0,
			}
		},
		onPullDownRefresh() {
			this.x=0
			this.y=0
			this.my_like = 0
			this.my_publish = 0
			this.like_list.splice(0,this.like_list.length)
			this.publish_list.splice(0,this.publish_list.length)
			let that = this;
			db.collection("love").where({
				like_user: getApp().globalData.avatarUrl
			}).get({
				success: res => {
					if (res.data.length != 0) {
						that.like_list.push(res.data)
						that.my_like = that.my_like + res.data.length
					}
					db.collection("buy").where({
						like_user: getApp().globalData.avatarUrl
					}).get({
						success: res => {
							if (res.data.length != 0) {
								that.like_list.push(res.data)
								that.my_like = that.my_like + res.data.length
							}
							db.collection("search").where({
								like_user: getApp().globalData.avatarUrl
							}).get({
								success: res => {
									if (res.data.length != 0) {
										that.like_list.push(res.data)
										that.my_like = that.my_like + res.data.length
									}
									getApp().globalData.like_list.splice(0,getApp().globalData.like_list.length)
									for(var i = 0; i < that.like_list.length; i++){
										for(var j = 0; j < that.like_list[i].length; j++){
											getApp().globalData.like_list[that.y] = that.like_list[i][j]
											that.y=that.y+1
										}
									}
								}
							})
						}
					})
				}
			})
			// console.log(getApp().globalData.like_list)
			db.collection("love").where({
				nickName: that.nickName,
				avatarUrl: that.avatarUrl,
			}).get({
				success: res => {
					if (res.data.length != 0) {
						that.publish_list.push(res.data)
						that.my_publish = that.my_publish + res.data.length
					}
					db.collection("buy").where({
						nickName: that.nickName,
						avatarUrl: that.avatarUrl,
					}).get({
						success: res => {
							if (res.data.length != 0) {
								that.publish_list.push(res.data)
								that.my_publish = that.my_publish +res.data.length
							}
							db.collection("search").where({
								nickName: that.nickName,
								avatarUrl: that.avatarUrl,
							}).get({
								success: res => {
									if (res.data.length != 0) {
										that.publish_list.push(res.data)
										that.my_publish =that.my_publish +res.data.length
									}
									getApp().globalData.publish_list.splice(0,getApp().globalData.publish_list.length)
									for (var i = 0; i <that.publish_list.length; i++) {
										for (var j =0; j < that.publish_list[i].length; j++) {
											getApp().globalData.publish_list[that.x] =that.publish_list[i][j]
											that.x =that.x + 1
										}
									}
								}
							})
						}
					})
				}
			})
		},
		methods: {
			BindGetUserInfo: function() {
				if (this.loginState == 1) {

				} else {
					let that = this;
					wx.getUserProfile({
						desc: '正在获取',
						success: function(res) {
							// console.log(res.userInfo)
							that.nickName = res.userInfo.nickName
							that.avatarUrl = res.userInfo.avatarUrl
							that.loginState = 1
							getApp().globalData.nickName = that.nickName
							getApp().globalData.avatarUrl = that.avatarUrl
							getApp().globalData.loginState = that.loginState
							db.collection("user").where({
									nickName: that.nickName,
									avatarUrl: that.avatarUrl,
								})
								.get({
									success: res => {
										if (res.data.length == 0) {
											// console.log("用户还没登记过")
											db.collection("user").add({
												data: {
													nickName: that.nickName,
													avatarUrl: that.avatarUrl,
												}
											})
										} else {
											// console.log("用户已经登录过了")
										}
									},
								})

							db.collection("love").where({
								like_user: getApp().globalData.avatarUrl
							}).get({
								success: res => {
									if (res.data.length != 0) {
										that.like_list.push(res.data)
										that.my_like = that.my_like + res.data.length
									}
									db.collection("buy").where({
										like_user: getApp().globalData.avatarUrl
									}).get({
										success: res => {
											if (res.data.length != 0) {
												that.like_list.push(res.data)
												that.my_like = that.my_like + res.data.length
											}
											db.collection("search").where({
												like_user: getApp().globalData.avatarUrl
											}).get({
												success: res => {
													if (res.data.length != 0) {
														that.like_list.push(res.data)
														that.my_like = that.my_like + res.data.length
													}
													for(var i = 0; i < that.like_list.length; i++){
														for(var j = 0; j < that.like_list[i].length; j++){
															getApp().globalData.like_list[that.y] = that.like_list[i][j]
															that.y=that.y+1
														}
													}
												}
											})
										}
									})
								}
							})

							db.collection("love").where({
								nickName: that.nickName,
								avatarUrl: that.avatarUrl,
							}).get({
								success: res => {
									if (res.data.length != 0) {
										that.publish_list.push(res.data)
										that.my_publish = that.my_publish + res.data.length
									}
									db.collection("buy").where({
										nickName: that.nickName,
										avatarUrl: that.avatarUrl,
									}).get({
										success: res => {
											if (res.data.length != 0) {
												that.publish_list.push(res.data)
												that.my_publish = that.my_publish +res.data.length
											}
											db.collection("search").where({
												nickName: that.nickName,
												avatarUrl: that.avatarUrl,
											}).get({
												success: res => {
													if (res.data.length != 0) {
														that.publish_list.push(res.data)
														that.my_publish =that.my_publish +res.data.length
													}
													for (var i = 0; i <that.publish_list.length; i++) {
														for (var j =0; j < that.publish_list[i].length; j++) {
															getApp().globalData.publish_list[that.x] =that.publish_list[i][j]
															that.x =that.x + 1
														}
													}
												}
											})
										}
									})
								}
							})
						},
						fail: function(res) {
							console.log('您拒绝了请求');
							return;
						}
					})
				}
				console.log(getApp().globalData.like_list)
			},
			about: function() {
				wx.showModal({
					title: '这是成理陈铭做的第一个小程序',
					showCancel: false,
				})
			},
			connect: function() {
				wx.showModal({
					title: '请联系QQ：1357124742',
					showCancel: false,
				})
			},
			Publish_list: function() {
				wx.navigateTo({
					url: "../publish_list/publish_list"
				})
			},
			Like_list: function() {
				wx.navigateTo({
					url: "../like_list/like_list"
				})
			}
		}
	}
</script>

<style>
	page {
		background: #F5F5F5;
	}

	.meMain {
		width: 100%;
		position: relative;
	}

	.meMain>image {
		width: 100%;
		height: 220upx;
		display: block;
	}

	.meMainBox {
		width: 92%;
		margin: 0 auto;
	}

	.mePosition {
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
	}

	.meHead {
		overflow: hidden;
		margin: 25rpx;
		position: relative;
	}

	.meHeadAvatar {
		float: left;
		width: 19%;
		margin-top: 10upx;
	}

	.meHeadAvatar image {
		width: 110upx;
		height: 110upx;
		display: block;
		border-radius: 50%;
	}

	.meHeadName {
		float: left;
		width: 75%;
		line-height: 120upx;
		color: #FFFFFF;
		font-size: 28upx;
		overflow: hidden;
		text-overflow: ellipsis;
		white-space: nowrap;
		margin-top: 5rpx;
		margin-left: 5rpx;
	}

	.meOverBg {
		background: #FFFFFF;
		overflow: hidden;
		border-radius: 12upx;
		margin-top: 30upx;
	}

	.meVisitor {
		display: flex;
		flex-direction: row;
	}

	.meVisitorLt {
		width: 50%;
		text-align: center;
		margin: 30upx 0;
	}

	.meVisitorLt:nth-child(1) {
		border-left: 1px #F5F5F5 solid;
		border-right: 1px #F5F5F5 solid;
	}

	.meVisitorTxt_01 {
		font-size: 28upx;
		color: #666666;
	}

	.meVisitorTxt_01 image {
		width: 35upx;
		height: 35upx;
		vertical-align: middle;
		margin: 0 10upx 4upx 0;
	}

	.meVisitorTxt_02 {
		font-size: 30upx;
		color: #3B7ED5;
	}

	.meVisitorTitle {
		font-size: 30upx;
		color: #333333;
		border-bottom: 1px #F5F5F5 solid;
		padding: 20upx 30upx;
	}

	.meOrderLt {
		width: 25%;
		text-align: center;
		padding: 30upx 0;
		transition: all 0.4s;
	}

	.meOrderLt:active {
		background: #E2E2E2;
	}

	.meOrderTxt_01 {}

	.meOrderTxt_01 image {
		width: 40upx;
		height: 40upx;
		display: block;
		margin: 0 auto 6upx;
	}

	.meOrderTxt_02 {
		font-size: 24upx;
		color: #666666;
	}

	.businessList {
		overflow: hidden;
		padding: 30upx;
		border-bottom: 1px #F5F5F5 solid;
		transition: all 0.4s;
	}

	.businessList:active {
		background: #E2E2E2;
	}

	.businessList:last-child {
		border-bottom: none;
	}

	.businessListTxt {
		float: left;
		font-size: 28upx;
		color: #333333;
	}

	.businessListTxt image {
		width: 35upx;
		height: 35upx;
		vertical-align: middle;
		margin: 0 10upx 4upx 0;
	}

	.businessListYou {
		float: right;
		font-size: 28upx;
		color: #333333;
	}

	.businessListYou image {
		width: 20upx;
		height: 20upx;
		vertical-align: middle;
		margin: 0 0 4upx 10upx;
	}
</style>
