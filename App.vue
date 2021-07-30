<script>
	export default {
		globalData: {
			loginState: '',
			avatarUrl: '../../static/icon/avatar.png',
			nickName: '点击登录',
			token: '',
		    publish_list:[],
			like_list:[],
		},
		onLaunch: function() {
			wx.getUserProfile({
				desc: '正在获取',
				success:res=>{
					console.log(res)
				}
			})
		},
		onShow: function() {},
		onHide: function() {},
	}
</script>

<style>
	@import "./colorui/main.css";
	@import "./colorui/icon.css";
	@import 'colorui/animation.css';

	.round-click {
		height: 120rpx;
		width: 120rpx;
		background-color: #ED1C24;
		border-radius: 100%;
		position: fixed;
		bottom: 150rpx;
		right: 20rpx;
		display: flex;
		align-items: center;
		justify-content: center;
		z-index: 9;
		margin-right: 30rpx;
	}

	.round-click navigator {
		font-size: 40rpx;
		max-width: 80rpx;
		color: #000000;
		text-align: center;
	}
</style>
