<template>
	<view class="uni-flex uni-column container">
		<view class="item-newNN">
			<input class="uni-input" type="text" placeholder="请输入新的密码:" v-model="user.password1" required="required" />
			<input class="uni-input" type="text" placeholder="请再次输入新的密码:" v-model="user.password2" required="required" />
			<button class="btn-basic orange-gradient" @tap="updatePassword(user)">确认修改</button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				user: {
					password1: '',
					id: uni.getStorageSync('login_key').userId
				},
				modifyPassword: uni.getStorageSync('login_key').password
			};
		},
		methods: {
			updatePassword: function(user) {
				var _this = this;
				uni.request({
					url: this.apiServer + '/user/updatePassword',
					method: 'PUT',
					data: {
						password: _this.user.password1,
						id: _this.user.id
					},
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
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
