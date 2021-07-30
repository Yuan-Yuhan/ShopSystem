<template>
	<view>
		<van-cell-group>

			<van-field name="phone" label="手机号" type="number" maxlength="11" title-width="3em" required clearable
				:value="phone" placeholder="请输入手机号" readonly />
			<van-field name="username" label="用户名" title-width="3em" placeholder="如:张三" required clearable
				:value="username" readonly />
			<van-field name="gender" label="性别" title-width="3em" placeholder="请输入性别" clearable :value="gender"
				@change="onChangeGender" />
			<van-field name="birthday" label="生日" title-width="3em" placeholder="请输入生日" clearable :value="birthday"
				@change="onChangeBirthday" />
			<van-field name="place" label="所在地" title-width="3em" placeholder="请输入所在地" clearable :value="place"
				@change="onChangePlace" />
		</van-cell-group>
		<van-button color="linear-gradient(to right, #4bb0ff, #6149f6)" @click="update" >
			确认提交</van-button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				phone: uni.getStorageSync('user').phone,
				username: uni.getStorageSync('user').nikename,
				gender: uni.getStorageSync('user').gender,
				birthday: uni.getStorageSync('user').birthday,
				place: uni.getStorageSync('user').place,
			}
		},
		methods: {
			onChangeGender(event) {
				this.$data.gender = event.detail
			},
			onChangeBirthday(event) {
				this.$data.birthday = event.detail
			},
			onChangePlace(event) {
				this.$data.place = event.detail
			},

			update() {
			uni.request({
					url: 'http://47.98.134.114:8081/user/update',
					method: 'GET',
					data: {
						uid: uni.getStorageSync('user').uid,
						gender: this.$data.gender,
						birthday: this.$data.birthday,
						place: this.$data.place,

					},
					success: res => {

						console.log(res, '打印RES哈哈哈')
						uni.setStorageSync('user', res.data.data);
					}
				});
				uni.switchTab({
					url:'/pages/my/index'
				})
			}
		}
	}
</script>

<style>
</style>
