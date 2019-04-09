<template>
	<view class="container">
		<view class="top">
			<view class="avatar-box">
				<image src="../../static/default.png" mode="scaleToFill" class="avatar" v-if="!storageData.login">
				</image>
				<image :src="storageData.avatar" mode="scaleToFill" class="avatar" v-if="storageData.login"></image>
			</view>
			<view class="header">
				<view class="info-box">
					<navigator url="../signin/signin" v-if="!storageData.login" class="login">点击登录</navigator>
					<text v-if="storageData.login" class="name">{{ storageData.nickname }}</text>
				</view>
				<view class="mes-box" v-if="storageData.login">

					<navigator url="../setting/setting">
						<text type="primary" @tap="signOut" class="setting">个人设置</text>
					</navigator>
				</view>
			</view>
		</view>
		<view class="content" v-if="storageData.login">
			<view class="card">
				<text>{{articleNumb}}</text>
				<br>
				<text>文章</text>
			</view>
			<view class="card">
				<text>{{concernNumb}}</text>
				<br>
				<text>关注</text>
			</view>
			<view class="card">
				<text>{{infoNumb}}</text>
				<br>
				<text>消息</text>
			</view>
			<view class="card">
				<text>{{jifenNumb}}</text>
				<br>
				<text>积分</text>
			</view>
		</view>
		<view class="item" v-for="(list, index) in lists" :key="index" v-if="storageData.login">
			<text class="articleName">{{list.articleName}}</text>
		</view>

	</view>
</template>

<script>
	var loginRes, _self;
	export default {
		data() {
			return {
				storageData: {},
				articleNumb: '10',
				concernNumb: '5',
				infoNumb: '66',
				jifenNumb: '100',
				lists: [{
						id: '1',
						articleName: '吐血推荐！5款好用又骚气的神战！真的有毒！（附网址）'
					},
					{
						id: '2',
						articleName: '5款APP神器，一个都不能错过（影视、音乐、阅读、工具、学习）'
					},
					{
						id: '3',
						articleName: '对于程序员来说最难写的是代码吗？'
					},
					{
						id: '4',
						articleName: '坚持打卡50天，我被朋友圈大部分的人屏蔽了'
					}
				]
			};
		},
		onLoad: function() {},
		onShow: function() {
			const loginKey = uni.getStorageSync('login_key');
			console.log("come");
			if (loginKey) {
				console.log(loginKey);
				this.storageData = {
					login: loginKey.login,
					nickname: loginKey.nickname,
					avatar: loginKey.avatar
				};
			} else {
				this.storageData = {
					login: false
				}
			}
		},
		methods: {

		}
	};
</script>

<style scoped>
	.top {
		display: flex;
		flex-direction: column;
		height: 70px;
		margin-top: 5px;
	}

	.avatar-box {
		text-align: center;
	}

	.info-box {
		display: flex;
		align-items: center;
		justify-content: flex-start;
	}

	.header {
		display: flex;
		text-align: center;
	}

	.name {
		margin-left: 180upx;
		margin-top: 45upx;
	}

	.setting {
		margin-left: 60upx;
		color: green;
	}

	.content {
		margin-top: 80upx;
		display: flex;
		flex-direction: row;
		margin-left: 50upx;
	}

	.card {
		text-align: center;
		font-size: 40upx;
		width: 150upx;
		height: 110upx;
		border-right: 1upx solid #EEEEEE;
	}

	.item {
		height: 150upx;
		border-bottom: 1upx solid #EEEEEE;
		margin-top: 30upx;
	}

	.articleName {
		margin-bottom: 50upx;
		color: #8F8F94;
	}

	.login {
		margin-left: 285upx;
		margin-top: 60upx;
	}
</style>
