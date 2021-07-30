<template>

	<view class="content">
		<form @reset="zc">
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
					<input class="input" name="nikename" v-model="nikename" placeholder="请输入用户名" />
				</view>
				<view class="inputWrapper">
					<input class="input" type="password" name="password" v-model="password" placeholder="请输入密码" />
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
				phone: '',
				nikename:'',
				password: ''
			}
		},
		onLoad() {

		},
		methods: {
		zc(){
			const data = {
				phone: this.phone,
				password: this.password,
				nikename:this.nikename
			};
			uni.request({
				url: 'http://47.98.134.114:8081/user/register',
				method: 'GET',
				data: {
					phone: this.phone,
					password: this.password,
					nikename:this.nikename
				},
				success: res => {
					uni.showToast({
						title: '注册成功',
						duration: 2000,
						icon: 'success'
					})}
			})
			uni.navigateTo({
				url:'/pages/login/index'
			})
		}
		}

	}
</script>

<style>
	.content {
		background: #f6f5ec;
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

