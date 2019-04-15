<template>
	<view class="container">
		<view class="article-box">
			<view class="article" v-for="(article,index1) in articles" :key="index1">
				<!-- 标题 -->
				<text class="article-title" @tap="gotoDetail(article.id)">{{article.title}}</text>
				<!-- 大于等于三张图片的显示方式 -->
				<view class="images" v-if="article.imgs.length >= 3">
					<view class="thumbnail-box">
						<view class="thumbnail-item" v-for="(item,index) in article.imgs" :key="index" v-if="index < 3">
							<image :src="item.imgs"></image>
							{{item.imgUrl}}
						</view>
					</view>
				</view>
				<!-- 小于三张图片的显示方式 -->
				<view class="images" v-else-if="article.imgs.length >= 1">
					<view class="text-img-box">
						<view class="left">
							<text>{{handleContent(article.content)}}...</text>
						</view>
						<view class="right">
							<image :src="article.imgs[article.imgs.length -1].imgs"></image>
						</view>
					</view>
				</view>
				<!-- 没有图片的显示方式 -->
				<view class="text-box" v-else>
					<text>{{handleContent(article.content)}}...</text>
				</view>
				<!-- 文章作者等信息 -->
				<view class="article-info">
					<image :src="article.avatar" class="avatar small"></image>
					<text class="info-text">{{article.nickname}}</text>
					<text class="info-text">{{handleTime(article.createTime)}}</text>
				</view>
			</view>
		</view>
		<view>
			<navigator url="../write/write" hover-class="navigator-hover" v-if="login" @tap="islogin()">
				<button class="btn-floating orange-gradient"><text class="pluss">+</text></button>
			</navigator>
			<navigator url="../signin/signin" hover-class="navigator-hover" @tap="islogin()" v-else>
				<button class="btn-floating orange-gradient"><text class="pluss">+</text></button>
			</navigator>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				articles: [],
				login: false
			};
		},
		onLoad: function() {
			this.getArticles();
		},
		onShow: function() {
			const loginKey = uni.getStorageSync('login_key');
			console.log(loginKey);
			if (loginKey) {
				this.login = true;
			} else {
				this.login = false;
			}
		},
		onPullDownRefresh: function() {
			this.getArticles();
		},
		methods: {
			getArticles: function() {
				var _this = this;
				uni.request({
					url: this.apiServer + '/article/list',
					method: 'GET',
					header: {
						'content-type': 'application/json'
					},
					success: res => {
						_this.articles = res.data.data;
					},
					complete: function() {
						uni.stopPullDownRefresh();
					}
				});
			},
			gotoDetail: function(aId) {
				uni.navigateTo({
					url: '../article_detail/article_detail?aId=' + aId
				});
			},
			handleTime: function(date) {
				var d = new Date(date);
				var year = d.getFullYear();
				var month = d.getMonth() + 1;
				var day = d.getDate() < 10 ? '0' + d.getDate() : '' + d.getDate();
				var hour = d.getHours() < 10 ? '0' + d.getHours() : '' + d.getHours();
				var minutes = d.getMinutes() < 10 ? '0' + d.getMinutes() : '' + d.getMinutes();
				var seconds = d.getSeconds() < 10 ? '0' + d.getSeconds() : '' + d.getSeconds();
				return year + '-' + month + '-' + day + ' ' + hour + ':' + minutes + ':' + seconds;
			},
			handleContent: function(content) {
				content = content.replace(/(\n)/g, '');
				content = content.replace(/(\t)/g, '');
				content = content.replace(/(\r)/g, '');
				content = content.replace(/<\/?[^>]*>/g, '');
				content = content.replace(/\s*/g, '');
				return content.substring(0, 50);
			},
			islogin: function() {
				if (this.login) {
					console.log('已登录');
				} else {
					console.log('未登录');
				}
			}
		}
	};
</script>

<style>
	.article {
		display: flex;
		flex-direction: column;
		border-bottom: 10px solid #EEEEEE;
		margin-top: 15px;
		padding-bottom: 15px;
	}

	.avatar-box {
		display: flex;
		flex-direction: row;
	}

	.article-title {
		font-weight: 900;
		margin-bottom: 5px;
	}

	.text-img-box {
		display: flex;
		flex-direction: row;
	}

	.thumbnail-box {
		display: flex;
		flex-direction: row;
	}

	.thumbnail-item image {
		width: 220upx;
		height: 220upx;
		margin-left: 7px;
	}

	.left {
		flex: 1 1 60%;
		display: -webkit-box;
		-webkit-box-orient: vertical;
		-webkit-line-clamp: 4;
		overflow: hidden;
	}

	.right {
		flex: 1 1 40%;
		width: 100%;
		height: 100%;
	}

	.right image {
		width: 90%;
		height: 200upx;
	}

	.article-info {
		color: grey;
		width: 100%;
		margin-left: 10upx;
		margin-top: 10px;
	}

	.avatar {
		width: 75upx;
		height: 75upx;
		border-radius: 50%;

	}

	.info-text {
		margin-left: 15px;
	}

	/* 橘色渐变色按钮 */
	.orange-gradient {
		background: linear-gradient(40deg, #ffd86f, #fc6262);
		color: #FFF;
	}

	/* 圆形按钮 */
	/* .btn-floating {
		/* 阴影效果，四个参数分别是：水平阴影位置、垂直阴影位置、阴影尺寸位置大小、阴影颜色 */
	/* box-shadow: 2px 5px 10px #AAA;
	width: 50px;
	height: 50px;
	border-radius: 50%;
	cursor: pointer;
	position: relative;
	margin-left: 300px;
	} */

	.btn-floating {
		position: fixed;
		bottom: 100px;
		right: 20px;
		width: 50px;
		height: 50px;
		border-radius: 50%;
		background-color: #de533a;
		/* background-image: url(../../static/Pencil32.png); */
		box-shadow: 2px 5px 10px #aaa;
		cursor: pointer;
		border: none;
		outline: none;
		display: flex;
		justify-content: center;
		align-items: center;
		color: #ffffff;
	}

	.pluss {
	}
</style>
