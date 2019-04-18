<template>
	<view class="container">
		<view>
			<button class="btn"></button>
		</view>
		<view class="mask" id="mask">
			<view class="login" id="login">

			</view>
		</view>
	</view>

</template>

<script>
	export default {
		data() {

		},
		methods: {
			mask: function() {
				var a = document.getElementsByTagName("a")[0];
				var mask = document.getElementById("mask");
				a.onclick = function(event) {
					mask.style.display = "block";
					//阻止冒泡
					event = event || window.event;
					if (event || event.stopPropagation()) {
						event.stopPropagation();
					} else {
						event.cancelBubble = true;
					}
				}

				document.onclick = function(event) {
					event = event || window.event;
					//兼容获取触动事件时被传递过来的对象
					var aaa = event.target ? event.target : event.srcElement;
					if (aaa.id !== "login") {
						mask.style.display = "none";
					}

				}
			}
		}
	};
</script>

<style>
	.btn {
		width: 100px;
		height: 40px;
		background: #09BB07;
	}

	.mask {
		width: 100%;
		height: 100%;
		position: fixed;
		top: 0;
		left: 0;
		display: none;
		background-color: rgba(0, 0, 0, 0.6);
	}

	.mask .login {
		width: 500px;
		height: 350px;
		background-color: #fff;
		margin: 150px auto;
	}
</style>
