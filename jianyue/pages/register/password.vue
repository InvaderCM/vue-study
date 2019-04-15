<template>
	<view class="container">
		<input class="uni-input" password type="text" placeholder="输入密码" v-model="password" required="required" />
		<button class="register-btn" @tap="signUp(userDTO)">注册</button>
		<navigator url="../signin/signin" class="toSignin">现在就登录</navigator>
	</view>
</template>

<script>
export default {
	data() {
		return {
			userDTO: {
				mobile: '',
				password: ''
			}
		};
	},
	onLoad: function(option) {
		//option为object类型，会序列化上个页面传递的参数
		console.log(option.mobile);
		this.mobile = option.mobile;
	},
	methods: {
		signUp: function(userDTO) {
			var _this = this;
			uni.request({
				url: this.apiServer + '/user/sign_up',
				// url: 'http://192.168.137.1:8080/api/user/sign_up',
				method: 'POST',
				header: { 'content-type': 'application/json' },
				data: {
					mobile: _this.mobile,
					password: _this.password
				},
				success: res => {
				  	if (res.data.code === 0) {
	                uni.showModal({
						title: '提示',
						content: '注册成功'
					})
					uni.navigateTo({
						
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
	.toSignin{
		color: #F76260;
		text-align: center;
		margin-top: 10px;
	}
</style>