<template>
	<view class="container">
		<view class="item">
			<view class="item-left"></view>
			<view class="item-right">
				<button type="button" class="btn-basic" @click="postArticle()">发布</button>
			</view>
		</view>
		<view class="article">
			<view class="article-title">
				<input type="text" v-model="title" placeholder="请输入标题" class="title" />
			</view>
			<view class="article-image">
				<button class="imageg-btn" @tap="chooseImg()">添加图片</button>
			</view>
			<view class="article-content">
				<textarea v-model="content1" placeholder="请输入正文" class="content" />
				</view>
			<view class="article-yulan">
				<text class="yulan">预览区：</text>
				<!-- 使用graceUI的富文本解析功能，来预览文章内容 -->
				<view class="grace-text">
					<text class="grace-title">{{title}}</text>
					<br/>
					<text class="grace-content">{{content1}}</text>
					<rich-text :nodes="content" bindtap="postArticle"></rich-text>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			title: '',
			content: '',
			content1:'',
			userId: uni.getStorageSync('login_key').userId,
			imgs: []
		};
	},
	onLoad() {},
	methods: {
		// 选择图片上传方法
		chooseImg: function() {
			var _this = this;
			uni.chooseImage({
				count: 1,
				sizeType: ['original', 'compressed'],
				sourceType: ['album'],
				success: function(res) {
					console.log(JSON.stringify(res.tempFilePaths));
					uni.uploadFile({
						url: _this.apiServer +'/avatar/upload',
						// url: 'http://10.30.162.205:8080/api/avatar/upload',
						filePath: res.tempFilePaths[0],
						name: 'file',
						success: uploadFileRes => {
							//图片上传成功，回显图片地址
							console.log(uploadFileRes.data);
							//将图片地址加入imgs数组
							_this.imgs.push(uploadFileRes.data);
							//将图片地址拼接HTML标签，加入文章内容
							_this.content += '<img src="' + uploadFileRes.data + '" width = "100%"/>';
						}
					});
				}
			});
		},
		// 发布文章方法
		postArticle: function() {
			var _this = this;
			uni.request({
				url: this.apiServer + '/article/add',
				// url: 'http://10.30.162.205:8080/api/article/add',
				method: 'POST',
				header: { 'content-type': 'application/x-www-form-urlencoded' },
				data: {
					uId: this.userId,
					title: this.title,
					content: '<div>' + this.content +'</div>'
				},
				success: res => {
					if (res.data.code === 0) {
						//获得发布文章成功返回的文章id
						var aId = res.data.data;
						console.log(aId);
						uni.showToast({
							title: '发布成功'
						});
						//将文章id和文章对应的图片地址数组传到后台，存入数据库
						uni.request({
							url: this.apiServer + '/img/add',
							method: 'POST',
							header: { 'content-type': 'application/x-www-form-urlencoded' },
							data: {
								aId: aId,
								imgs: JSON.stringify(_this.imgs)  //序列化imgs数组
							},
							success: res => {
								if (res.data.code === 0) {
									console.log('文章图片地址已写入数据库');
								}
							}
						});
						uni.switchTab({
							url: '../index/index'
						});
					}
				}
			});
		}
	}
};
</script>
<style>
	
.item{
	display: flex;
	justify-content: space-between;
}
.item-left{
	flex: 1 80%;
}
.item-right{
	flex: 1 20%;
}
.imageg-btn{
	background: linear-gradient(40deg, #ffd86f, #fc6262);
	width: 95%;
	height: 40px;
	margin: 10px;
	border-radius: .3rem;
	padding: 0;
	cursor: pointer;
	border: none;
	font-size: 17px;
	color: #FFFFFF;
}
/* 基础按钮 */
.btn-basic{
	background: linear-gradient(40deg, #ffd86f, #fc6262);
	width: 90px;
	height: 40px;
	margin: 10px;
	border-radius: .3rem;
	padding: 0;
	cursor: pointer;
	border: none;
	font-size: 17px;
	color: #FFFFFF;
}
.article-content{
	margin-top: 20px;
	margin-bottom: 10px;
	border-bottom: 3px solid #EEEEEE;
}
.article-title{
	border-bottom: 3px solid #EEEEEE;
	padding-bottom: 20px;
}
.article-image{
	margin-top: 10px;
}
.yulan{
	font-size: 20px;
	font-weight: 900;
}
.grace-title{
	font-size: 20px;
	font-weight: 900;
}
.grace-content{
	font-size: 15px;
}
</style>
