<template>
	<view class="container">
		<!-- 顶部头像和昵称区域，纵向排列 -->
		<view class="top">
			<view class="avatar-box">
				<image src="../../static/default.png" mode="scaleToFill" class="avatar" v-if="!storageData.login"></image>
				<image :src="storageData.avatar" mode="scaleToFill" class="avatar" v-if="storageData.login"></image>
			</view>
			<view class="info-box">
				<view class="left">
					<navigator url="../signin/signin" v-if="!storageData.login">点击登录</navigator>
					<text v-if="storageData.login">{{ storageData.nickname }}</text>
					<navigator url="../setting/setting" v-if="storageData.login">
						<text class="setting-txt">个人设置</text>
					</navigator>
				</view>
				<view class="leftright3">
			<view class="left3"></view>
			<view class="right3">
				<button class="qiandao" type="primary" @tap="showShade">
					<text style="">签到</text>
				</button>
			</view>
			<view class="grace-shade" v-if="show">
				<view class="grace-shade-msg grace-box-shadow overlay">
					<view class="close-btn grace-icons icon-close3 " @tap="closeShade()">关闭asdsadasdasd</view>
					<view class="body1">
						<text class="grace-icons icon-eye " style="margin-right:10px;"></text>
						<image src="../../static/head2.jpg" class="tu"/>
						<button @tap="closeShade()"class="fanhui">签到成功(点击返回)</button>
						
					</view>
				</view>
			</view>
		</view>
			</view>
		</view>

		<!-- 中间文章数量、好友数量、消息数量等统计区域，横向排列 -->
		<view v-if="login">
			<scroll-view class="grace-tab-title grace-center" scroll-x="true" id="grace-tab-title">
				<view v-for="(cate, index) in categories" :key="index" :data-cateid="cate.cateid" :data-index="index" :class="[cateCurrentIndex == index ? 'grace-tab-current' : '']"
				 @tap="tabChange">
					{{ cate.name }}
				</view>
			</scroll-view>

			<view class="demo-content">
				<!-- 文章部分 -->
				<view class="content" v-if="cateCurrentIndex == 0">
					<text class="article-count">您共发表了 {{ articles.length }} 篇文章</text>
					<view class="list">
						<view class="list-item" v-for="(article, index) in articles" :key="index">
							</navigator>
							<text @tap="gotoDetail(article.id)">
								{{ index + 1 }} . {{ article.title }}
							</text>
						</view>
					</view>
				</view>

				<!-- 关注部分 -->
				<view class="content" v-if="cateCurrentIndex == 1">
					<view class="list">
						<text class="article-count">您共关注了 {{ follows.length }} 个人</text>
						<view class="list-item1" v-for="(follow, index) in follows" :key="index">
							<view class="item-avatar">
								<image :src="follow.avatar" class="avatar small"></image>
							</view>
							<view class="item-nickname">
								<text style="margin-left: 20px;">{{ follow.nickname }}</text>
							</view>
						</view>
					</view>
				</view>

				<!-- 收藏部分 -->
				<view class="content" v-if="cateCurrentIndex == 2">
					<text class="article-count">您共收藏了 {{ collects.length }} 篇文章</text>
					<view class="list">
						<view class="list-item" v-for="(collect, index) in collects" :key="index">
							<text>
								{{ index + 1 }} . {{ collect.title }}
							</text>
						</view>
					</view>
				</view>

				<!-- 积分部分 -->
				<view class="content" v-if="cateCurrentIndex == 3">
					<text>积分 : {{ jifen }}</text>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	var loginRes, _self;
	export default {
		data() {
			return {
				show:false,
				storageData: {
					userId: 0,
					nickname: '',
					avatar: '',
					login: false
				},
				//分类信息
				categories: [{
						cateid: 0,
						name: '文章'
					},
					{
						cateid: 1,
						name: '关注'
					},
					{
						cateid: 2,
						name: '收藏'
					},
					{
						cateid: 3,
						name: '积分'
					}
				],
				// 当前选择的分类
				cateCurrentIndex: 0,
				articles: [],
				follows: [],
				jifen: 100,
				collection: 8
			};
		},
		onLoad: function() {},
		onShow: function() {
			var _this = this;
			uni.request({
				// url: 'http://localhost:8080/api/user/' + uni.getStorageSync('login_key').userId,
				url: this.apiServer + '/user/getUserByMobile',
				method: 'GET',
				data: {
					mobile: _this.mobile
				},
				header: {
					'content-type': 'application/x-www-form-urlencoded'
				},
				success: res => {
					if (res.data.code === 0) {
						// console.log(res.data.data.avatar + '————————————');
						_this.avatar = res.data.data.avatar;
						_this.nickname = res.data.data.nickname1;
					}
				}
			});
			const loginKey = uni.getStorageSync('login_key');
			if (loginKey) {
				this.login = true;
				// console.log('shihoudengnkw:' + this.login);
				this.storageData = {
					login: loginKey.login,
					nickname: loginKey.nickname,
					avatar: loginKey.avatar,
					userId: loginKey.userId
				};
				// 			uni.request({
				// 				// 获取用户的文章数量
				// 				url: this.apiServer + '/article/getArticleByUID',
				// 				method: 'GET',
				// 				header: { 'content-type': 'application/x-www-form-urlencoded' },
				// 				data: {
				// 					userId: this.storageData.userId
				// 				},
				// 				success: res => {
				// 					_this.articleCount = res.data.data.length;
				// 					console.log('文章数量' + _this.articleCount);
				// 				}
				// 			});
				uni.request({
					// 根据用户id获取所有发表的文章
					url: this.apiServer + '/article/getArticleByUID',
					method: 'GET',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						uId: this.storageData.userId
					},
					success: res => {
						_this.articles = res.data.data;
					}
				});
				uni.request({
					// 	根据userId查询所有关注的用户
					url: this.apiServer + '/follow/list',
					method: 'GET',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						fromUId: this.storageData.userId
					},
					success: res => {
						_this.follows = res.data.data;
					}
				});
				uni.request({
					// 	根据userId查询所有收藏的文章
					url: this.apiServer + '/collect/collectlist',
					method: 'GET',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					data: {
						fromUId: this.storageData.userId
					},
					success: res => {
						_this.collects = res.data.data;
					}
				});
			} else {
				this.storageData = {
					login: false
				};
			}
		},
		methods: {
			back: function() {
				uni.navigateBack();
			},
			showShade: function() {
				this.show = true;
			},
			closeShade: function() {
				this.show = false;
			},
			tabChange: function(e) {
				// 选中的索引
				var index = e.currentTarget.dataset.index;
				// 具体的分类id
				var cateid = e.currentTarget.dataset.cateid;
				this.cateCurrentIndex = index;
				// 动态替换内容
				this.content = this.categories[index].name;
			},
			gotoDetail: function(aId) {
				uni.navigateTo({
					url: '../articles_detail/article_detail?aId=' +
						aId +
						'&userId=' +
						this.storageData.userId
				});
			},
			mask: function() {
				this.$refs.mask.style.height = doc.getElementById('app').clientHeight + 'px'
			}

		}
	};
</script>

<style scoped>
	@import "../../graceUI/animate.css";
	.demo {
		display: flex;
		flex-wrap: nowrap;
		border-left: 1px solid #F8F8F8;
	}
	
	.demo view {
		width: 100%;
		text-align: center;
		padding: 10upx 2%;
		border-bottom: 1px solid #F8F8F8;
		border-right: 1px solid #F8F8F8;
	}
	.leftright3 {
		display: flex;
		justify-content: space-between;
		align-items: center;
	}
	.overlay {
		position: fixed;
		top: 0;
		right: 0;
		bottom: 0;
		left: 0;
		z-index: 9;
		background-color: rgba(0, 0, 0, .8);
	}
	.body1{
		display: flex;
		flex-direction: column;
		align-items: center;
	}
	.tu{
		width: 90%;
		margin: 0 auto;
		height: 400px;
	}
	.fanhui{
		width: 60%;
		margin-top: 10px;
		background: #09BB07;
		color: #FFFFFF;
		position: relative;
	}
	.top {
		display: flex;
		flex-direction: row;
		height: 80px;
		margin-top: 20px;
	}

	.avatar-box {
		display: flex;
		flex: 1 10%;
		justify-content: center;
	}

	.avatar {
		width: 60px;
		height: 60px;
	}

	.info-box {
		display: flex;
		justify-content: space-between;
		flex: 1 60%;
	}

	.setting-txt {
		color: #00b26a;
		margin-left: 15px;
	}

	.center {
		display: flex;
		justify-content: center;
		margin-top: 30px;
	}

	.content {
		margin-top: 20px;
		flex: 1 1 25%;
	}

	.list-item {
		margin-top: 20px;
		display: flex;
		border-bottom: 1px solid #EEEEEE;
		padding-bottom: 10px;
	}

	.list-item1 {
		margin-top: 20px;
		display: flex;
		border-bottom: 1px solid #EEEEEE;
		padding-bottom: 5px;
	}

	.article-count {
		font-size: 12px;
		font-weight: 600;
	}

	.item-nickname {
		font-weight: 500;
		font-size: 16px;
	}

	.btn {
		background: linear-gradient(40deg, #ffd86f, #fc6262);
		color: #FFF;
		width: 100%;
		height: 50px;
		border-radius: 10px;
		padding: 0;
		cursor: pointer;
		border: none;
		font-size: 15px;
	}

	.txt-btn {
		text-align: center;
	}

	.left {
		flex: 1 50%;
	}

	.right {
		flex: 1 50%;
	}
	.overlay {
		position: fixed;
		top: 0;
		right: 0;
		bottom: 0;
		left: 0;
		z-index: 9;
		background-color: rgba(0, 0, 0, .8);
	}
	.qiandao{
		background: #09BB07;
	}
</style>
