<template>
	<view>
		<view class="data">
			<text>{{place}}</text> <br />
			<text>{{address}}</text><br />
			<text>{{name }}</text><br />
			<text>{{phone}}</text>

		</view>

		<van-card v-bind:num="num" v-bind:price="price" v-bind:title="title" v-bind:desc="desc" v-bind:thumb="thumb" />

		</van-card>

		<van-cell-group>
			<van-cell title="配送" value="韵达快递" />
			<van-cell title="运费" value="￥6" />
			<van-field label="留言" placeholder="请输入您的留言" type="text" input-align="right" />
			<van-cell title="支付方式" value="微信支付" is-link />
		</van-cell-group>
		<van-submit-bar v-bind:price="totalPrice" button-text="提交订单" @submit="onSubmit" />
	</view>
</template>

<script>
	export default {
		data() {
			return {
				num: '1',
				place: uni.getStorageSync('addressInfo').acceptPlace,
				address: uni.getStorageSync('addressInfo').detailedAddress,
				name: uni.getStorageSync('addressInfo').acceptPerson,
				phone: uni.getStorageSync('addressInfo').acceptPhone,
				price: uni.getStorageSync('goodsForOrder').price,
				title: uni.getStorageSync('goodsItemForOrder').gname,
				desc: uni.getStorageSync('goodsForOrder').name,
				thumb: uni.getStorageSync('goodsForOrder').gpic,
				totalPrice: (uni.getStorageSync('goodsForOrder').price + 6) * 100

			}
		},
		onLoad() {},

		methods: {
			onSubmit() {
				uni.showToast({
						title: '订单提交成功',
						duration: 2000,
						icon: 'success'
					})
					
					// let data2 = JSON.parse(data1)

					uni.request({
						url: 'http://47.98.134.114:8081/order/addOrder',
						method: 'GET',
						data: {
							"uid": uni.getStorageSync('UID'),
							"sid": uni.getStorageSync('goodsForOrder').sid,
							"gid": uni.getStorageSync('goodsForOrder').id,
							"merchantId": uni.getStorageSync('goodsItemForOrder').merchantId,
							"goodsCount": 1,
											
						},
						success: res => {
							console.log(res, '打印Res')

						}
					})
			}
		}
	}
</script>

<style>
</style>
