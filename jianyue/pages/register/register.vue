<template>
	<view class="container">
		<view class="item-input">
			<view class="left">
				<input class="uni-input left" type="number" placeholder="输入手机号" v-model="mobile" required="required" />
			</view>
			<view class="right">
				<button type="primary" class="green-btn small-btn right" :disabled="disabled" @tap="getVerifyCode">
					{{ title }}
				</button>
			</view>
			
		</view>
		<input class="uni-input" type="number" placeholder="输入验证码" v-model="verifyCode" required="required" />
		<button @tap="checkCode" class="register-btn" :disabled="this.nextdisabled">下一步</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				mobile: '',
				verifyCode: '',
				timer: 60,
				disabled: false,
				title: '发送验证码',
				nextdisabled: false
			};
		},
		onLoad() {},
		methods: {
			getVerifyCode: function() {
				var _this = this;
				if (_this.mobile.length < 11) {
					uni.showToast({
						title: '手机号不符合要求',
						duration: 2000,
						icon: 'none'
					});
					return;
				} else {
					_this.disabled = true;
					let timer1 = setInterval(() => {
						_this.timer--;
						_this.title = _this.timer + '秒';
						if (_this.timer == 0) {
							clearInterval(timer1);
							_this.timer = 60;
							_this.disabled = false;

							// _this.nextdisabled = false;

							_this.title = '发送验证码';
							return;
						}
					}, 1000);
					uni.request({
						url: _this.apiServer + '/user/verify',
						method: 'POST',
						header: {
							'content-type': 'application/x-www-form-urlencoded'
						},
						data: {
							mobile: _this.mobile
						},
						success: res => {
							if (res.data.code === 0) {
								uni.showToast({
									title: '验证码已发送'
								});
								_this.disabled = true;
								console.log(_this.disabled);
							} else {
								uni.showModal({
									title: '提示',
									content: res.data.msg
								});
							}
						}
					});
				}
			},
			checkCode: function() {
				var _this = this;
				console.log(_this.verifyCode);
				console.log(_this.mobile);
				uni.request({
					url: this.apiServer + '/user/check',
					method: 'POST',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						mobile: _this.mobile,
						verifyCode: _this.verifyCode
					},
					success: res => {
						console.log(res.data.code);
						if (res.data.code === 0) {
							uni.navigateTo({
								url: '../register/password?mobile=' + _this.mobile
							});
						} else {
							uni.showModal({
								title: '提示',
								content: res.data.msg
							});
						}
					}
				});
			}
		}
	};
</script>

<style>
	.item-input{
		display: flex;
		justify-content: space-between;
		margin-top: 5px;
	}
	.small-btn {
		background: linear-gradient(40deg, #ffd86f, #fc6262);
		color: #FFFFFF;
		width: 100%;
	}
	.register-btn{
		background: linear-gradient(40deg, #ffd86f, #fc6262);
		color: #FFFFFF;
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
