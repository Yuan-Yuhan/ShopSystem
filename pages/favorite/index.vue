<template>
	<view class="catContent">
		<view class="cards">
			<view class="checkedStyle">
				<van-swipe-cell :right-width="65" :name="item.gname" :key="index" v-for="(item,index) in cardGoodsList">
					<van-card :price="formatPrice(item.price)" :desc="item.desc" :title="item.title" class="goods-card"
						:thumb="item.thumb">
					</van-card>
					<van-button slot="right" @click="cardDelete(item.id, index)" type="danger" class="delete-button">删除
					</van-button>
				</van-swipe-cell>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				//商品列表s
				cardGoodsList: [],
				selectedCartId: 0,
				flag: true
			}
		},
		onLoad() {
			this.getGoodsList()
		},

		methods: {
			//计算价格
			formatPrice(price) {
				return (price).toFixed(2);
			},

			//删除商品事件
			cardDelete(id, index) {
				uni.request({
					url: 'http://47.98.134.114:8081/favorite/delete',
					method: 'GET',
					data: {
						favoriteId: item,
					},
					success: res => {
						console.log(res.data, '喜羊羊');
						uni.showToast({
							title: '删除成功',
							duration: 2000,
							icon: 'success'
						})
					}
				})
				this.cardGoodsList.splice(index, 1);
			},
			// 获取缓存store中收藏数据
			getGoodsList() {
				uni.request({
					url: 'http://47.98.134.114:8081/favorite/find',
					method: 'GET',
					data: {
						uid: uni.getStorageSync('user').uid,
					},
					success: res => {
						console.log(res, 'shabi');
						//得到收藏商品sid

						//发起请求通过sid
						res.data.data.forEach(item => {
							uni.request({
								url: 'http://47.98.134.114:8081/goodsItem/findBySid',
								method: 'GET',
								data: {
									sid: item
								},
								success: res => {
									var cartItemData = {
										id: item.cartId,
										title: res.data.data.gname,
										desc: res.data.data.details,
										price: res.data.data.lowestprice,

										thumb: res.data.data.spic
									}

									this.cardGoodsList.push(cartItemData)

								}
							}) //请求规格中断

						}) // foreach中断


					} //success中断
				}) //购物车uni.request中断
			},
		},
		mounted() {
			// 进入该页面就要执行的方法；用于加载后台数据
		}
	}
</script>

<style>
	.catContent {
		width: 100%;
		height: 100%;
		margin: 0 auto;
	}

	.checkedStyle {
		width: 100%;
		height: 90%;
		margin-left: 5px;
		margin-bottom: 14%;
	}

	.cards {
		width: 100%;
		height: 100%;
	}

	.submitBar {
		width: 100%;
		height: 10%;
	}

	.chengboxs {
		width: 20px;
		height: 80px;
		margin-top: 55px;
		float: left
	}

	.goods-card {
		width: 92%;
		float: right;
	}

	.checkedStyle .van-checkbox__label {
		width: 100%;
	}

	.checkedStyle .van-checkbox {
		padding-bottom: 5px;
	}

	.checkedStyle van-button .van-button--normal {
		width: 100%;
		height: 126px;
	}
</style>
