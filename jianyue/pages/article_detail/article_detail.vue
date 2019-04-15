<template>
	<view class="container">
		<text class="article-title">{{ article.title }}</text>
		<view class="article-info">
			<view class="article-info-left">
				<image :src="article.avatar" class="avatar small"></image>
				<text article-nickname>{{ article.nickname }}</text>
				<br />
				<text class="info-text">{{ handleTime(article.createTime) }}</text>
			</view>
			<view class="article-info-right">
				<!-- 登录用户和文章作者不是同一个人，就显示关注或取消关注按钮 -->
				<button v-if="userId != article.uId && !followed" class="follow-btn" @tap="follow">关注</button>
				<button v-if="userId != article.uId && followed" class="follow-btn cancel" @tap="cancelFollow">取消</button>
			</view>
		</view>

		<view class="grace-text" style="margin-top: 10px;">
			<rich-text :nodes="article.content" bindtap="tap"></rich-text>
		</view>
		<view class="article-collect">
			<!-- 登录用户和文章作者不是同一个人，就显示关注或取消关注按钮 -->
			<button v-if="userId != article.uId && !collected" class="collect-btn" @tap="collect">收藏</button>
			<button v-if="userId != article.uId && collected" class="collect-btn cancel" @tap="cancelCollect">取消收藏</button>
		</view>
		<text class="info-text">评论({{ comments.length }})</text>
		<view class="comment-item" v-for="(comment, index) in comments" :key="index">
			<view class="left">
				<image :src="comment.avatar" class="avatar small"></image>
			</view>
			<view class="right">
				<text>{{ comment.nickname }}</text>
				<br />
				<text class="comment-content">{{ comment.content }}</text>
				<view>
					<text style="margin-right: 10px;">{{ comments.length - index }}楼</text>
					<text>{{ handleTime(comment.commentTime) }}</text>
				</view>
			</view>
		</view>
		<input class="uni-input comment-box" type="text" placeholder="写下你的评论" v-model="content" required="required" />
		<button class="commit-btn" @tap="send">提交</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				article: {
					aId: 0,
					uId: 0,
					title: '',
					content: '',
					avatar: '',
					nickname: '',
					createTime: ''
				},
				comments: [],
				content: '',
				userId: uni.getStorageSync('login_key').userId,
				followed: false,
				collected: false
			};
		},
		onLoad: function(option) {
			//option为object类型，会序列化上个页面传递的参数
			this.article.aId = option.aId;
		},
		onShow: function() {
			this.getArticle();
		},
		onPullDownRefresh: function() {
			this.getArticles();
		},
		methods: {
			getArticle: function() {
				var _this = this;
				uni.request({
					url: this.apiServer + '/article/' + this.article.aId,
					method: 'GET',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						userId: this.userId
					},
					success: res => {
						_this.article.aId = res.data.data.article.id;
						_this.article.uId = res.data.data.article.uId;
						_this.article.title = res.data.data.article.title;
						_this.article.content = res.data.data.article.content;
						_this.article.nickname = res.data.data.article.nickname;
						_this.article.avatar = res.data.data.article.avatar;
						_this.article.createTime = res.data.data.article.createTime;
						_this.comments = res.data.data.comments;
						if (res.data.data.followed === '已关注') {
							_this.followed = true;
						}
					},
					complete: function() {
						uni.stopPullDownRefresh();
					}
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
			send: function() {
				console.log('评论人编号：' + this.userId + ',文章编号：' + this.article.aId + '，评论内容：' + this.content);
				uni.request({
					url: this.apiServer + '/comment/add',
					method: 'POST',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						aId: this.article.aId,
						uId: this.userId,
						content: this.content
					},
					success: res => {
						if (res.data.code === 0) {
							uni.showToast({
								title: '评论成功'
							});
							this.getArticle();
							this.content = '';
						}
					}
				});
			},
			follow: function() {
				uni.request({
					url: this.apiServer + '/follow/add',
					method: 'POST',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						fromUId: this.userId,
						toUId: this.article.uId
					},
					success: res => {
						// console.log(this.userId);
						// console.log(this.article.uId);
						if (res.data.code === 0) {
							uni.showToast({
								title: '关注成功'
							});
							this.followed = true;
						}
					}
				});
			},
			cancelFollow: function() {
				uni.request({
					url: this.apiServer + '/follow/cancel',
					method: 'POST',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						fromUId: this.userId,
						toUId: this.article.uId
					},
					success: res => {
						if (res.data.code === 0) {
							uni.showToast({
								title: '已取消关注'
							});
							this.followed = false;
						}
					}
				});
			},
			collect: function() {
				uni.request({
					url: this.apiServer + '/collect/add',
					method: 'POST',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						fromUId: this.userId,
						toAId: this.article.aId
					},
					success: res => {
						if (res.data.code === 0) {
							uni.showToast({
								title: '收藏成功'
							});
							this.collected = true;
						}
					}
				});
			},
			cancelCollect: function() {
				uni.request({
					url: this.apiServer + '/collect/cancel',
					method: 'POST',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						fromUId: this.userId,
						toAId: this.article.aId
					},
					success: res => {
						if (res.data.code === 0) {
							uni.showToast({
								title: '已取消收藏'
							});
							this.collected = false;
						}
					}
				});
			}
		}
	};
</script>

<style>
	.container {
		margin-top: 10px;
		margin-left: 10px;
		margin-right: 10px;
	}

	.article-title {
		font-weight: bold;
	}

	.article-info {
		color: grey;
		display: flex;
		justify-content: space-between;
		margin-top: 5px;
	}

	.avatar {
		border-radius: 50%;
		width: 75upx;
		height: 75upx;
	}

	.comment-item {
		display: flex;
		margin-top: 15upx;
	}

	.comment-box {
		margin-top: 20px;
	}

	.left {
		margin-top: 50upx;
	}

	.right {
		margin-left: 40upx;
		color: grey;
	}

	.info-text {
		margin-left: 5px;
	}
	
	.article-nickname {
		margin-left: 10px;
	}

	.follow-btn {
		background: linear-gradient(40deg, #ffd86f, #fc6262);
		height: 33px;
		width: 80px;
		font-size: 12pt;
		text-align: center;
		cursor: pointer;
		color: #FFFFFF;
	}
	.collect-btn{
		background: linear-gradient(40deg, #ffd86f, #fc6262);
		color: #FFFFFF;
		width: 40%;
		border-radius: 20px;
		margin-top: 10px;
		margin-bottom: 10px;
	}
	.commit-btn{
		background: linear-gradient(40deg, #ffd86f, #fc6262);
		color: #FFFFFF;
		width: 100%;
		margin-top: 10px;
		margin-bottom: 10px;
	}
	.comment-content {
		color: black;
	}
</style>
