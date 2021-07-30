<template>

	<view class="content">
		<form @submit="bindLogin" @reset="zc">
			<view class="avatorWrapper">
				<view class="avator">
					<image class="img" src="/static/logo.jpg" mode="widthFix"></image>
				</view>
			</view>
			<view class="form">
				<view class="inputWrapper">
					<input class="input" name="phone" v-model="phone" placeholder="请输入手机号" />
				</view>
				<view class="inputWrapper">
					<input class="input" type="password" name="password" v-model="password" placeholder="请输入密码" />
				</view>
				<view class="loginBtn">
					<button class="btnValue" type="default" formType="submit">登录</button>
				</view>
				<view class="zcBtn">
					<button class="zc" type="default" formType="reset">注册</button>
				</view>
			</view>
		</form>
	</view>
</template>
<script>
	export default {
		data() {
			return {
				title: 'Hello',
				phone: '123',
				password: '123'
			}
		},
		onLoad() {

		},
		methods: {
			bindLogin() {
				const data = {
					phone: this.phone,
					password: this.password
				};

				console.log(data);
				uni.request({
					url: 'http://47.98.134.114:8081/user/login',
					method: 'GET',
					data: {
						phone: this.phone,
						password: this.password
					},
					success: res => {
						console.log(res, '打印Res')

						if (res.data.code == 0) {
							uni.setStorageSync('user', res.data.data);
							uni.setStorageSync('UID', res.data.data.uid);
							uni.setStorageSync('PHONE', res.data.data.phone);
							uni.setStorageSync('NAME', res.data.data.nikename);
							uni.setStorageSync('AVATAR', res.data.data.avatar);

							uni.showToast({
								title: '登录成功',
								duration: 2000,
								icon: 'none'
							})
							uni.switchTab({
								url: '/pages/index/index'
							})
						}
					},
					fail: () => {

					},
					complete: () => {}
				});
			},
		    zc(){
				uni.navigateTo({
					url:'../zc/index'
				})
			}
		}

	}
</script>

<style>
	.content {
		background:#feeeed;
		width: 100vw;
		height: 100vh;
	}

	.avatorWrapper {
		height: 30vh;
		width: 100vw;
		display: flex;
		justify-content: center;
		align-items: flex-end;
	}

	.avator {
		width: 350upx;
		height: 350upx;
		overflow: hidden;
	}

	.avator .img {
		width: 100%
	}

	.form {
		padding: 0 100upx;
		margin-top: 80px;
	}

	.inputWrapper {
		width: 100%;
		height: 80upx;
		background: white;
		border-radius: 20px;
		box-sizing: border-box;
		padding: 0 20px;
		margin-top: 25px;
	}

	.inputWrapper .input {
		width: 100%;
		height: 100%;
		text-align: center;
		font-size: 15px;
	}

	.loginBtn {
		text-align: center;
		color: #EAF6F9;
		font-size: 10px;
		margin-top: 20px;

	}

	.zcBtn {
		text-align: center;
		color: #EAF6F9;
		font-size: 10px;
		margin-top: 20px;

	}
</style>
