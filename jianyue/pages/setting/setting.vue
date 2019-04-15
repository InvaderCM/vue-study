<template>
	<view class="uni-flex uni-column container">
		<view class="list">
			<navigator class="list-item" url="article"><text class="setting-txt">文章推送</text></navigator>
			<navigator class="list-item" url="newMessage"><text class="setting-txt">新消息推送</text></navigator>
			<navigator class="list-item" url="../user_info/user-info"><text class="setting-txt">个人资料</text></navigator>
		</view>
		<button @tap="logout" class="btn-basic orange-gradient">退出当前账号</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {};
		},
		onLoad() {},
		methods: {
			logout: function() {
				console.log('log out');
				uni.removeStorageSync('login_key');
				uni.showToast({
					title: '已经退出当前账号'
				});
				uni.navigateBack();
			}
		}
	};
</script>

<style>
	.green-btn {
		background-color: #00B26A;
		margin-top: 10px;
		color: #FFFFFF;
	}

	.list-item {
		display: flex;
		align-items: center;
		height: 50px;
	}

	/* 橘色渐变色按钮 */
	.orange-gradient {
		background: linear-gradient(40deg, #ffd86f, #fc6262);
		color: #FFF;
	}

	/* 基础按钮 */
	.btn-basic {
		width: 100%;
		height: 50px;
		margin-top: 15px;
		border-radius: 10px;
		padding: 0;
		cursor: pointer;
		border: none;
		font-size: 20px;
	}
</style>
