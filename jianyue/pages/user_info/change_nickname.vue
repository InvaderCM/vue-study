<template>
	<view class="uni-flex uni-column container">
		<view class="item-nick">当前昵称：{{ modifyNickName }}</view>

		<view class="item-newNN">
			<input class="uni-input" type="text" placeholder="请输入新的昵称:" v-model="user.nickname1" required="required" />

			<button class="btn-basic orange-gradient" @tap="updateNickName(user)">确认修改</button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				user: {
					nickname1: '',
					id: uni.getStorageSync('login_key').userId
				},
				modifyNickName: uni.getStorageSync('login_key').nickname
			};
		},
		methods: {
			updateNickName: function(user) {
				// const loginKey = uni.getStorageSync('login_key');
				var _this = this;
				uni.request({
					// url: 'http://192.168.137.1:8080/api/user/updateNickName',
					url: this.apiServer+'/user/updateNickName',
					method: 'PUT',
					data: {
						nickname: _this.user.nickname1,
						// nickname:uni.getStorageSync('login_key').nickname,
						// id: uni.getStorageSync('login_key').id
						id: _this.user.id
					},
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					// 				success: function() {
					// 					console.log(_this.user.id+"-----"+_this.user.nickname1+"成功");
					// 				}
					success: res => {
						uni.navigateBack();
					}
				});
			}
		}
	};
</script>

<style>
	.item-nick {
		margin-top: 10px;
		font-size: 15px;
	}

	.item-newNN {
		margin-top: 20px;
	}

	input {
		height: 50px;
		border-bottom: 1px solid #eee;
		margin-bottom: 5px;
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
