<template>
	<view class="myContent">
		<view class="header">
			<van-image width="100%" height="100%" class="headbgImage" src="/static/images/img/background.png">
			</van-image>
			<van-image width="100%" height="100%" round class="avatar" v-bind:src="photoSrc" @click="chooseImage">
				<!-- QAQ -->
			</van-image>
			<text class="myInformation">{{name}}</text>

		</view>
		<view class="middle-num">
			<view class="oner">
				<view class="zi">
					<text class="zier">{{coupon}}<text class="zisan">张</text></text>
					<text class="quan">优惠券</text>
				</view>
			</view>
			<view class="twoer">|</view>
			<view class="three">
				<view class="zi">
					<text class="zier">{{integral}}\n</text>
					<text class="quan">积分</text>
				</view>
			</view>
		</view>
		<view class="mydingdan">
			<view class="row1">
				<van-cell is-link url="/pages/orderlist/index?active=all" title="订单内容" value="全部订单"
					link-type="navigateTo" />
			</view>
			<view class="zhifu">
				<van-grid :gutter="3" square column-num="5">
					<van-grid-item v-for="(item,key) in icon" :key="key" :url="item.toUrl" :text="item.text"
						:icon="item.photoSrc" />
				</van-grid>
			</view>
		</view>
		<view class="liebiao">
			<van-cell is-link v-for="(liebiao,key) in lbIcon" :key="key" :icon="liebiao.photoSrc" :title="liebiao.text"
				:url="liebiao.toUrl">
				<van-icon slot="right-icon" class="custom-icon" />
			</van-cell>
		</view>
	</view>

</template>

<script>
	export default {
		data() {
			return {
				coupon: 6, //优惠券数量
				integral: 300, //积分数量
				icon: [],
				lbIcon: [],
				name: uni.getStorageSync('NAME'),
				photoSrc: uni.getStorageSync('AVATAR') //初始值QAQ
			}
		},
		onLoad() {
			//支付导航
			this.tabBarIcon();
			//列表单元格
			this.liebiaoIcon();

		},
		methods: {
			/* 支付导航 */
			tabBarIcon() {
				var data = {
					"icons": [{
							"photoSrc": "/static/icon/all.png",
							"text": "全部",
							"toUrl": "/pages/orderlist/index?active=all"
						},

						{
							"photoSrc": "/static/icon/dfk.png",
							"text": "待付款",
							"toUrl": "/pages/orderlist/index?active=dfk"
						},
						{
							"photoSrc": "/static/icon/dfh.png",
							"text": "待发货",
							"toUrl": "/pages/orderlist/index?active=dfh"
						},
						{
							"photoSrc": "/static/icon/dsh.png",
							"text": "待收货",
							"toUrl": "/pages/orderlist/index?active=dsh"
						},
						{
							"photoSrc": "/static/icon/ywc.png",
							"text": "已完成",
							"toUrl": "/pages/orderlist/index?active=ywc"
						},

					]
				};
				this.icon = data.icons
			},
			chooseImage() {
				uni.chooseImage({ // 从本地相册选择图片或使用相机拍照。
					count: 1, //默认选择1张图片
					sizeType: ['original', 'compressed'], //original 原图，compressed 压缩图，默认二者都有
					sourceType: ['album'],
					success: (res) => { //start
						console.log(res.tempFilePaths[0], 11111); //成功则返回图片的本地文件路径列表 tempFilePaths
						uni.uploadFile({ //将本地资源上传到开发者服务器
							url: 'http://47.98.134.114:8081/user/uploadAvatar', //接口地址
							filePath: res.tempFilePaths[0], //图片地址
							name: 'file',
							formData: {
								'uid': uni.getStorageSync('UID')
							},

							success: (res) => {
								// console.log(res.上传成功)
								let data = JSON.parse(res.data)
								console.log(res.data, 'sosososososososoo');
								if (data.code == 0) {
									this.$data.photoSrc = data.data.fileDownloadUri //改变QAQ
								}


							}
						});
					} //end
				});
			},

			liebiaoIcon() {
				var data = {
					"icons": [{
							"photoSrc": "/static/icon/mydata.png",
							"text": "资料编辑",
							"toUrl": "/pages/mydata/index"
						},
						{
							"photoSrc": "/static/icon/wdsc.png",
							"text": "我的收藏",
							"toUrl": "/pages/favorite/index"
						},
						{
							"photoSrc": "/static/icon/dzgl.png",
							"text": "地址管理",
							"toUrl": "/pages/address/index"
						}


					]
				};
				this.lbIcon = data.icons
			},
		}
	}
</script>

<style>
	page {
		background: #eaeaea;
	}

	.myContent {
		width: 100%;
		height: 100%;
		margin: 0 auto;
	}

	.header {
		flex-direction: row;
		display: flex;
		height: 200rpx;
	}

	.headbgImage {
		height: 25%;
		width: 100%;
		position: absolute;
		z-index: 1;
	}

	.avatar {
		height: 90rpx;
		width: 90rpx;
		position: absolute;
		z-index: 2;
		margin-left: 3%;
		margin-top: 3%;
	}

	.myInformation {
		position: absolute;
		z-index: 2;
		margin-left: 16%;
		margin-top: 9%;
		color: #FFFFFF;
	}

	.middle-num {
		position: absolute;
		z-index: 2;
		display: flex;
		flex-wrap: nowrap;
		background: white;
		width: 95%;
		margin-left: 20rpx;
		height: 160rpx;
		border-radius: 10px;
		margin-top: -50rpx;
	}

	.oner {
		flex-grow: 1;
		display: flex;
		align-items: center;
		justify-content: center;
		width: 100%;
		height: 100%;
	}

	.twoer {
		flex-grow: 1;
		display: flex;
		align-items: center;
		justify-content: center;
		width: 100%;
		height: 100%;
		color: #eaeaea;
	}

	.three {
		flex-grow: 1;
		display: flex;
		align-items: center;
		justify-content: center;
		width: 100%;
		height: 100%;
	}

	.zier {
		font-size: 48rpx;
		color: #333333;
	}

	.zisan {
		font-size: 24rpx;
		color: #333333;
	}

	.quan {
		font-size: 24rpx;
		color: #999999;
	}

	.zi {
		display: flex;
		flex-direction: column;
		align-items: center;
	}

	.mydingdan {
		display: flex;
		flex-wrap: nowrap;
		background: #fff;
		width: 95%;
		height: 250rpx;
		margin-left: 20rpx;
		border-radius: 10px;
		margin-top: 130rpx;
		flex-direction: column;
	}

	.mydingdan .row1 .van-cell {
		position: inherit;
		border-radius: 10px 10px 0px 0px;
	}

	.zhifu {
		margin-top: 5rpx
	}

	.liebiao {
		height: 658rpx;
		align-items: center;
		flex-wrap: nowrap;
		background: #fff;
		width: 91%;
		border-radius: 10px;
		flex-direction: column;
		padding: 2%;
		margin: 20rpx 20rpx 10rpx 20rpx;
	}

	.liebiao .van-cell {
		height: 16%;
	}
</style>
