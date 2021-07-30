<template>
	<view>
		<van-cell-group>
			<van-field name="acceptPerson" required clearable label="收货人" title-width="3em" acceptPhoneholder="请输入收货人"
				:value="acceptPerson" @change="changename" />

			<van-field name="acceptPlace" label="地区" title-width="3em" acceptPhoneholder="如:'湖北省武汉市洪山区'"
				:value="acceptPlace" @change="changePlace" required clearable />

			<van-field name="acceptPhone" label="手机号" type="number" maxlength="11" title-width="3em"
				acceptPhoneholder="本人手机号码" required :value="acceptPhone" @change="changephone" clearable />
			<van-field name="detailedAddress" label="详细地址" title-width="3em" acceptPhoneholder="珞南街道武汉大学信息学部" required
				:value="detailedAddress" @change="changeaddress" clearable />
		</van-cell-group>
		<van-button color="linear-gradient(to right, #4bb0ff, #6149f6)" @click="upload" class= "center">
			更改收货地址</van-button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				acceptPerson: '',
				acceptPhone: '',
				acceptPlace: '',
				detailedAddress: '',
			};
		},
		onLoad() {
			uni.request({
				url: 'http://47.98.134.114:8081/address/getAddress',
				method: 'GET',
				data: {
					uid: uni.getStorageSync('user').uid,
				},
				success: res => {
					console.log(res.data.data, '哈哈')
					// uni.setStorageSync('PLACE',res.data.data)
					this.setAddressInfo(res.data.data)
				}
			})
		},
		methods: {
			changename(event) {
				this.$data.acceptPerson = event.detail
			},
			changeaddress(event) {
				this.$data.detailedAddress = event.detail
			},
			changePlace(event) {
				this.$data.acceptPlace = event.detail
			},
			changephone(event) {
				this.$data.acceptPhone = event.detail
			},

			upload() {
				uni.request({
					url: 'http://47.98.134.114:8081/address/addOne',
					method: 'GET',
					data: {
						uid: uni.getStorageSync('user').uid,
						acceptPerson: this.$data.acceptPerson,
						detailedAddress: this.$data.detailedAddress,
						acceptPhone: this.$data.acceptPhone,
						acceptPlace: this.$data.acceptPlace,

					},
					success: res => {
						console.log(res);
					}
				})

			},

			setAddressInfo(result) {
				this.acceptPerson = result.acceptPerson
				this.acceptPhone = result.acceptPhone
				this.acceptPlace = result.acceptPlace
				this.detailedAddress = result.detailedAddress
			}

		},

	}
</script>

<style>
    .center{
        left: 10rpx;
    }
</style>
