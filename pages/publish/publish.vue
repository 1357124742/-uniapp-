<template>
	<view>
		<scroll-view scroll-x class="bg-white nav text-center fixed" :style="[{top:CustomBar + 'px'}]">
			<view class="cu-item" :class="index==TabCur?'text-blue cur':''" v-for="(item,index) in tabNav" :key="index"
				@tap="tabSelect" :data-id="index">
				{{tabNav[index]}}
			</view>
		</scroll-view>
		<block v-if="TabCur==0">
			<view class="cu-form-group margin-top">
				<view class="title">相遇时间</view>
				<picker mode="time" :value="time" start="00:00" end="23:59" @change="TimeChange">
					<view class="picker">
						{{time}}
					</view>
				</picker>
			</view>
			<view class="cu-form-group">
				<view class="title">相遇日期</view>
				<picker mode="date" :value="date" start="2021-04-10" end="2022-04-10" @change="DateChange">
					<view class="picker">
						{{date}}
					</view>
				</picker>
			</view>
			<view class="cu-form-group">
				<view class="title">相遇地点</view>
				<input name="input" class="money" @input="place_input"></input>
			</view>

			</checkbox-group>
			<view class="cu-bar bg-white margin-top">
				<view class="action">
					图片上传
				</view>
				<view class="action">
					{{imgList.length}}/4
				</view>
			</view>
			<view class="cu-form-group">
				<view class="grid col-4 grid-square flex-sub">
					<view class="bg-img" v-for="(item,index) in imgList" :key="index" @tap="ViewImage"
						:data-url="imgList[index]">
						<image :src="imgList[index]" mode="aspectFill"></image>
						<view class="cu-tag bg-red" @tap.stop="DelImg" :data-index="index">
							<text class='cuIcon-close'></text>
						</view>
					</view>
					<view class="solids" @tap="ChooseImage" v-if="imgList.length<4">
						<text class='cuIcon-cameraadd'></text>
					</view>
				</view>
			</view>
			<view class="cu-form-group margin-top">
				<textarea maxlength="-1" :disabled="modalName!=null" @input="des_love"
					placeholder="请输入具体描述 (80字以内)"></textarea>
			</view>
			<view class="padding flex flex-direction">
				<button class="cu-btn bg-grey lg" bindsubmit='res' @tap="publish_love_img"
					v-show="checked">上传图片</button>
			</view>
			<view class="padding flex flex-direction">
				<button class="cu-btn bg-grey lg" bindsubmit='res' @tap="publish_love">确定发布</button>
			</view>
		</block>
		<block v-if="TabCur==1">
			<view class="cu-form-group <margin-top></margin-top>">
				<view class="title">丢失时间</view>
				<picker mode="time" :value="time_1" start="00:00" end="23:59" @change="TimeChange_1">
					<view class="picker">
						{{time_1}}
					</view>
				</picker>
			</view>
			<view class="cu-form-group">
				<view class="title">丢失日期</view>
				<picker mode="date" :value="date_1" start="2021-04-10" end="2022-04-10" @change="DateChange_1">
					<view class="picker">
						{{date_1}}
					</view>
				</picker>
			</view>
			<view class="cu-form-group">
				<view class="title">联系手机</view>
				<input name="input" class="money" @input="phone_number_input"></input>
			</view>
			</checkbox-group>
			<view class="cu-bar bg-white margin-top">
				<view class="action">
					图片上传
				</view>
				<view class="action">
					{{imgList_1.length}}/4
				</view>
			</view>
			<view class="cu-form-group">
				<view class="grid col-4 grid-square flex-sub">
					<view class="bg-img" v-for="(item,index) in imgList_1" :key="index" @tap="ViewImage_1"
						:data-url="imgList_1[index]">
						<image :src="imgList_1[index]" mode="aspectFill"></image>
						<view class="cu-tag bg-red" @tap.stop="DelImg_1" :data-index="index">
							<text class='cuIcon-close'></text>
						</view>
					</view>
					<view class="solids" @tap="ChooseImage_1" v-if="imgList_1.length<4">
						<text class='cuIcon-cameraadd'></text>
					</view>
				</view>
			</view>
			<view class="cu-form-group margin-top">
				<textarea maxlength="-1" :disabled="modalName!=null" @input="des_search"
					placeholder="请输入具体描述 (80字以内)"></textarea>
			</view>
			<view class="padding flex flex-direction">
				<button class="cu-btn bg-grey lg" bindsubmit='res' @tap="publish_search_img"
					v-show="checked_1">上传图片</button>
			</view>
			<view class="padding flex flex-direction">
				<button class="cu-btn bg-grey lg" bindsubmit='res' @tap="publish_search">确定发布</button>
			</view>
		</block>
		<block v-if="TabCur==2">
			</checkbox-group>
			<view class="cu-bar bg-white margin-top">
				<view class="action">
					图片上传
				</view>
				<view class="action">
					{{imgList_2.length}}/4
				</view>
			</view>
			<view class="cu-form-group">
				<view class="grid col-4 grid-square flex-sub">
					<view class="bg-img" v-for="(item,index) in imgList_2" :key="index" @tap="ViewImage_2"
						:data-url="imgList_2[index]">
						<image :src="imgList_2[index]" mode="aspectFill"></image>
						<view class="cu-tag bg-red" @tap.stop="DelImg_2" :data-index="index">
							<text class='cuIcon-close'></text>
						</view>
					</view>
					<view class="solids" @tap="ChooseImage_2" v-if="imgList_2.length<4">
						<text class='cuIcon-cameraadd'></text>
					</view>
				</view>
			</view>
			<view class="cu-form-group margin-top">
				<view class="title">期望金额</view>
				<input name="input" class="money" @input="money_input"></input>
			</view>
			<view class="cu-form-group margin-top">
				<textarea maxlength="-1" :disabled="modalName!=null" @input="des_buy"
					placeholder="请输入具体描述 (80字以内)"></textarea>
			</view>
			<view class="padding flex flex-direction">
				<button class="cu-btn bg-grey lg" bindsubmit='res' @tap="publish_buy_img"
					v-show="checked_2">上传图片</button>
			</view>
			<view class="padding flex flex-direction">
				<button class="cu-btn bg-grey lg" bindsubmit='res' @tap="publish_buy">确定发布</button>
			</view>
		</block>
	</view>
</template>

<script>
	const db = wx.cloud.database()
	export default {
		data() {
			return {
				checked: true,
				checked_1: true,
				checked_2: true,
				i: '',
				CustomBar: this.CustomBar,
				TabCur: 0,
				tabNav: ['表白捞人', '失物招领', '二手发布'],
				place: '',
				money: '',
				phone_number: '',
				time: '08:00',
				time_1: '08:00',
				date: '2021-04-11',
				date_1: '2021-04-11',
				imgList: [],
				imgList_1: [],
				imgList_2: [],
				image: [],
				image_1: [],
				image_2: [],
				image_test: [],
				modalName: null,
				multiIndex: [0, 0, 0],
				describe_love: '',
				describe_search: '',
				describe_buy: '',
				place: '',
				index: 0,
			};
		},
		methods: {
			tabSelect(e) {
				this.TabCur = e.currentTarget.dataset.id;
				this.scrollLeft = (e.currentTarget.dataset.id - 1) * 60
			},
			TimeChange(e) {
				this.time = e.detail.value
			},
			TimeChange_1(e) {
				this.time_1 = e.detail.value
			},
			DateChange(e) {
				this.date = e.detail.value
			},
			DateChange_1(e) {
				this.date_1 = e.detail.value
			},
			ChooseImage() {
				uni.chooseImage({
					count: 4, //默认9
					sizeType: ['compressed'], //可以指定是原图还是压缩图，默认二者都有 'original'
					sourceType: ['album'], //从相册选择
					success: (res) => {
						if (this.imgList.length != 0) {
							this.imgList = this.imgList.concat(res.tempFilePaths)
						} else {
							this.imgList = res.tempFilePaths
						}
					}
				});
			},
			ViewImage(e) {
				uni.previewImage({
					urls: this.imgList,
					current: e.currentTarget.dataset.url
				});
			},
			DelImg(e) {
				uni.showModal({
					title: '臭宝儿',
					content: '确定要删除这张照片吗？',
					cancelText: '点错了',
					confirmText: '确定删除',
					success: res => {
						if (res.confirm) {
							this.imgList.splice(e.currentTarget.dataset.index, 1)
						}
					}
				})
			},
			ChooseImage_1() {
				uni.chooseImage({
					count: 4, //默认9
					sizeType: ['compressed'], //可以指定是原图还是压缩图，默认二者都有
					sourceType: ['album'], //从相册选择
					success: (res) => {
						if (this.imgList_1.length != 0) {
							this.imgList_1 = this.imgList_1.concat(res.tempFilePaths)
						} else {
							this.imgList_1 = res.tempFilePaths
						}
					}
				});
			},
			ViewImage_1(e) {
				uni.previewImage({
					urls: this.imgList_1,
					current: e.currentTarget.dataset.url
				});
			},
			DelImg_1(e) {
				uni.showModal({
					title: '臭宝儿',
					content: '确定要删除这张照片吗？',
					cancelText: '点错了',
					confirmText: '确定删除',
					success: res => {
						if (res.confirm) {
							this.imgList_1.splice(e.currentTarget.dataset.index, 1)
						}
					}
				})
			},
			ChooseImage_2() {
				uni.chooseImage({
					count: 4, //默认9
					sizeType: ['compressed'], //可以指定是原图还是压缩图，默认二者都有
					sourceType: ['album'], //从相册选择
					success: (res) => {
						if (this.imgList_2.length != 0) {
							this.imgList_2 = this.imgList_2.concat(res.tempFilePaths)
						} else {
							this.imgList_2 = res.tempFilePaths
						}
					}
				});
			},
			ViewImage_2(e) {
				uni.previewImage({
					urls: this.imgList_2,
					current: e.currentTarget.dataset.url
				});
			},
			DelImg_2(e) {
				uni.showModal({
					title: '臭宝儿',
					content: '确定要删除这张照片吗？',
					cancelText: '点错了',
					confirmText: '确定删除',
					success: res => {
						if (res.confirm) {
							this.imgList_2.splice(e.currentTarget.dataset.index, 1)
						}
					}
				})
			},
			des_love(e) {
				if (e.detail.value.length < 80) {
					this.describe_love = e.detail.value
				}
			},
			des_search(e) {
				if (e.detail.value.length < 80) {
					this.describe_search = e.detail.value
				}
			},
			des_buy(e) {
				if (e.detail.value.length < 80) {
					this.describe_buy = e.detail.value
				}
			},
			place_input(e) {
				this.place = e.detail.value
			},
			phone_number_input(e) {
				this.phone_number = e.detail.value
			},
			money_input(e) {
				this.money = e.detail.value
			},
			publish_love_img() {
				if (this.imgList.length != 0) {
					for (var i = 0; i < this.imgList.length; i++) {
						wx.cloud.uploadFile({
							cloudPath: Date.now() + Math.ceil(Math.random() * 100000) + ".jpg",
							filePath: this.imgList[i],
						}).then(res => {
							this.image.push(res.fileID)
						})
						if (i == (this.imgList.length) - 1) {
							wx.showModal({
								title: '上传完毕',
								showCancel: false,
							})
							this.checked = false
						}
					}
				}
			},
			publish_love() {
				if (this.checked == false) {
					wx.showModal({
						title: '提示',
						content: '是否确定发布',
						success: res => {
							if (res.confirm) {
								db.collection("love").add({
									data: {
										show2: this.time,
										show3: this.place,
										show1: this.date,
										img: this.image,
										describe: this.describe_love,
										like_number: 0,
										like_user:[],
										comment_number: 0,
										nickName: getApp().globalData.nickName,
										avatarUrl: getApp().globalData.avatarUrl,
										type: 'love'
									}
								}).then(
									wx.showModal({
										title: '上传成功',
										showCancel: false,
										success: res => {
											wx.switchTab({
												url: '../love/love'
											})
										}
									})
								)
							} else if (res.cancel) {
								console.log('用户点击取消')
							}
						}
					})
				} else {
					wx.showModal({
						title: '请先上传图片',
						showCancel: false,
					})
				}
			},
			publish_search_img() {
				if (this.imgList_1.length != 0) {
					for (var i = 0; i < this.imgList_1.length; i++) {
						wx.cloud.uploadFile({
							cloudPath: Date.now() + Math.ceil(Math.random() * 100000) + ".jpg",
							filePath: this.imgList_1[i],
						}).then(res => {
							this.image_1.push(res.fileID)
						})
					}
					wx.showModal({
						title: '上传完毕',
						showCancel: false,
					})
					this.checked_1 = false
				}
			},
			publish_search() {
				if (this.checked_1 == false) {
					wx.showModal({
						title: '提示',
						content: '是否确定发布',
						success: res => {
							if (res.confirm) {
								db.collection("search").add({
									data: {
										show3: this.phone_number,
										show2: this.time_1,
										show1: this.date_1,
										img: this.image_1,
										describe: this.describe_search,
										like_number: 0,
										like_user:[],
										comment_number: 0,
										nickName: getApp().globalData.nickName,
										avatarUrl: getApp().globalData.avatarUrl,
										type: 'search'
									}
								}).then(wx.showModal({
									title: '上传成功',
									showCancel: false,
									success: res => {
										wx.switchTab({
											url: '../search/search'
										})
									}
								}))
							} else if (res.cancel) {
								console.log('用户点击取消')
							}
						}
					})
				} else {
					wx.showModal({
						title: '请先上传图片',
						showCancel: false,
					})
				}
			},
			publish_buy_img() {
				if (this.imgList_2.length != 0) {
					for (var i = 0; i < this.imgList_2.length; i++) {
						wx.cloud.uploadFile({
							cloudPath: Date.now() + Math.ceil(Math.random() * 100000) + ".jpg",
							filePath: this.imgList_2[i],
						}).then(res => {
							this.image.push(res.fileID)
						})
					}
					wx.showModal({
						title: '上传完毕',
						showCancel: false,
					})
					this.checked_2 = false
				}
			},
			publish_buy() {
				if (this.checked_2 == false) {
					wx.showModal({
						title: '提示',
						content: '是否确定发布',
						success: res => {
							if (res.confirm) {
								db.collection("buy").add({
									data: {
										img: this.image,
										show1: this.money,
										show2:'',
										show3:'',
										describe: this.describe_buy,
										like_number: 0,
										like_user:[],
										comment_number: 0,
										nickName: getApp().globalData.nickName,
										avatarUrl: getApp().globalData.avatarUrl,
										type: 'buy'
									}
								}).then(wx.showModal({
									title: '上传成功',
									showCancel: false,
									success: res => {
										wx.switchTab({
											url: '../buy/buy'
										})
									}
								}))
							} else if (res.cancel) {
								console.log('用户点击取消')
							}
						}
					})
				} else {
					wx.showModal({
						title: '请先上传图片',
						showCancel: false,
					})
				}
			}
		}
	}
</script>

<style>
	page {
		padding-top: 45px;
	}

	,
	.cu-form-group .title {
		min-width: calc(4em + 15px);
	}

	.money {
		text-align: right;
	}
</style>
