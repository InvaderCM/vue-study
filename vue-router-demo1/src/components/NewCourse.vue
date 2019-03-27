<template>
	<div class="container">
		<p>
			课程
			<span class="star">*</span>
		</p>
		<input type="text" placeholder="请输入班课名称" v-model="course.courseName" class="input-box" />
		<p>
			班级
			<span class="star">*</span>
		</p>
		<input type="text" placeholder="请输入班级" v-model="course.courseClass" class="input-box" />
		<p>
			类型
			<span class="star">*</span>
		</p>
		<br />
		<p class="checkbox">
			<input type="checkbox" />
			<span>学校课表班课（学校课表班课就是学校安排课程表里的正式班课）</span>
		</p>
		<p>设置班课封面</p>
		<div class="preview" @click="handleClick()">
			<img :src="course.imageUrl" class="cover" v-if="!show" />
			<img src="../assets/plus.png" class="icon-plus" v-if="show"></image>
			<input type="file" @change="getFile($event)" style="display: none;" id="coverFile" />
		</div>
		<div>
			<button @click="addCourse(course)" class="btn">确定</button>
			<button @click="addCourse(course)" class="btn1">取消</button>
		</div>
		
	</div>
</template>
<script>
	export default {
		name: 'NewCourse',
		data() {
			return {
				show: true,
				file: '',
				loginUserId: 1,
				course: {
					courseName: '',
					courseClass: '',
					imageUrl: ''
				}
			};
		},
		methods: {
			addCourse: function(course) {
				var _this = this;
				this.$http({
					method: 'post',
					url: 'http://localhost:8080/api/course',
					data: {
						userId: _this.loginUserId,
						courseName: course.courseName,
						courseClass: course.courseClass,
						cover: course.imageUrl,
						finished: 0
					}
				}).then(function() {
					alert('新增班课成功');
					_this.$router.push('/');
				});
			},
			//点击图片预览区，即模拟点击文件选择组件
			handleClick: function() {
				document.getElementById('coverFile').click();
			},
			//图片预览
			getFile: function() {
				this.file = event.target.files[0];
				var windowURL = window.URL || window.webkitURL;
				this.course.imageUrl = windowURL.createObjectURL(this.file);
				// alert(windowURL.createObjectURL(this.file));
				this.show = false;
			}
		}
	};
</script>
<style scoped>
	.container {
		display: flex;
		flex-direction: column;
		padding-top: 20px;
		padding-left: 100px;
		background-color: #fff;
		margin-top: 20px;
	}

	.input-box {
		width: 500px;
		height: 40px;
		margin-bottom: 40px;
		font-size: 14px;
	}

	.btn {
		margin-top: 20px;
		width: 120px;
		height: 40px;
		border: 2px solid rgb(0, 187, 221);
		background-color: #fff;
		border-radius: 8px;
		outline: none;
		color: rgb(0, 187, 221);
		font-size: 16px;
	}
	
	.btn1 {
		margin-top: 20px;
		width: 120px;
		height: 40px;
		border: 2px solid gray;
		background-color: #fff;
		border-radius: 8px;
		outline: none;
		color: gray;
		font-size: 16px;
	}

	.preview {
		width: 150px;
		height: 150px;
		border: 2px dashed #aaa;
		cursor: pointer;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.icon-plus {
		width: 70px;
		height: 70px;
	}

	.cover {
		width: 100%;
		height: 100%;
	}

	.star {
		color: red;
	}

	p {
		position: relative;
		margin-top: -10px;
	}
	.checkbox{
		color: #00BBDD;
	}
</style>
