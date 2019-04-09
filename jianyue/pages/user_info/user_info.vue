<template>
	<view class="uni-flex uni-column container">
		<view class="list">
			<navigator class="list-nick" url="change_nickname">
				<view class="list-item">
					昵称:
					<view class="item-nick">
						{{nickname}}
					</view>
				</view>
			</navigator>
			<view class="list-avatar">
				<navigator @tap="showActionSheet">头像:</navigator>
				<image :src="avatar" class="avatar" @tap="showActionSheet"></image>
			</view>
			<view class="list-item">
				<navigator class="list-item" url="user-password">密码:</navigator>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				nickname: uni.getStorageSync('login_key').nickname,
				avatar: uni.getStorageSync('login_key').avatar,
				userId: uni.getStorageSync('login_key').userId
			};
		},
		onLoad() {},
		methods: {
			showActionSheet: function() {
				console.log('show');
				var _this = this;
				uni.showActionSheet({
					itemList: ['拍照', '从相册选择'],
					success: function(res) {
						console.log('选中了第' + (res.tapIndex + 1) + '个按钮');
						//选择的是拍照功能
						if (res.tapIndex == 0) {
							uni.chooseImage({
								count: 1,
								sourceType: ['camera'],
								success: function(res) {
									uni.saveImageToPhotosAlbum({
										filePath: res.tempFilePaths[0],
										success: function() {
											console.log('save success');
											uni.uploadFile({
												url: 'http://10.30.247.95:8080/api/user/avatar',
												filePath: res.tempFilePaths[0],
												name: 'file',
												formData: {
													userId: _this.userId
												},
												success: uploadFileRes => {
													console.log(uploadFileRes.data);
													_this.avatar = uploadFileRes.data;
												}
											}).then(function() {
												alert('修改图像成功');
												_this.courses.splice(index, 1); //修改id为index的1条数据
											});
										}
									});
								}
							});
						}
						//从相册选择
						if (res.tapIndex == 1) {
							uni.chooseImage({
								count: 1, //默认9
								sizeType: ['original', 'compressed'], //可以指定是原图还是压缩图，默认二者都
								sourceType: ['album'], //从相册选择
								success: function(res) {
									console.log(JSON.stringify(res.tempFilePaths));
									uni.uploadFile({
										url: 'http://192.168.137.1:8080/api/user/avatar',
										filePath: res.tempFilePaths[0],
										name: 'file',
										formData: {
											userId: _this.userId
										},
										success: uploadFileRes => {
											console.log(uploadFileRes.data);
											_this.avatar = uploadFileRes.data;
										}
									});
								}
							});
						}
					},
					fail: function(res) {
						console.log(res.errMsg);
					}
				});
			}
		},
	};
</script>

<style>
	.green-btn {
		background-color: #00b26a;
	}

	.list-avatar {
		display: flex;
		align-items: center;
		height: 100px;
		border-bottom: 1upx solid #EEEEEE;
	}

	.avatar {
		border-radius: 50%;
		align-items: center;
		height: 80px;
		width: 80px;
		margin-left: 100upx;
		border-bottom: 1upx solid #EEEEEE;
	}

	.list-item {
		display: flex;
		align-items: center;
		margin-top: 20upx;
		padding-bottom: 20upx;
	}

	.item-nick {
		margin-left: 120upx;
	}
</style>
