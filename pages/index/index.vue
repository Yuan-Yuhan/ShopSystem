<template>
	<view id="qbsp" class="content">
		<view class="header">
			<!-- 下拉框 -->
			<view class="dropdown">
				<van-dropdown-menu active-color="#ee0a24">
					<van-dropdown-item @change="toGoodsList" :value="dropdownValue" :options="dropdownMenus" />
				</van-dropdown-menu>
			</view>
			<view class="search">
				<!-- 搜索框 -->
				<van-search :value="searchValue" name="name" shape="round" placeholder="请输入搜索关键词" show-action
					@search="onChange" @cancel="searchCancel" />
			</view>
		</view>
		<view class="contentMain">
			<view class="swiperPictures">
				<!--轮播图-->
				<!-- indicator-dots=""指示点 -->
				<swiper class="home-swiper" indicator-dots="true" :autoplay="autoplay" :interval="interval"
					:circular="circular" :duration="duration">
					<block v-for="(item,key) in lunboData" :key="key">
						<swiper-item>

							<image :src="item.imgurl" class="slide-image" />

						</swiper-item>
					</block>
				</swiper>
			</view>

			<view class="noticeBar">
				<van-notice-bar backgroundColor="#fff" speed="30" left-icon="/static/icon/laba.png" :text="text" />
			</view>
			<view class="tabBar">
				<!-- gutter是4个宫格之间的距离 -->
				<van-grid :gutter="10" column-num="4">
					<van-grid-item v-for="(item,key) in icon" :key="key" :url="item.toUrl" :text="item.text"
						:icon="item.photoSrc" />
				</van-grid>
			</view>
			<view class="arrondi">
				<view class="xsms" @click="seckill" data-id="0">
					<image src="../../static/images/img/xsms.png"></image>
				</view>
				<view class="arrondiRight">
					<view class="xsmsRight" @click="giftPack" data-id="1">
						<image src="../../static/images/img/yhqzq.png"></image>
					</view>
					<view class="xsmsRight" @click="giftPack" data-id="2">
						<image src="../../static/images/img/tclb.png"></image>
					</view>
				</view>
			</view>
			<!-- 人气商品 -->
			<view id="rqsp" class="title">
				<image src="../../static/images/img/rqsp.png"></image>
			</view>

			<!-- 人气商品商品列表 -->
			<view class="goodsList">
				<van-grid :column-num="2" :border="false">
					<!-- 列数和间距 -->
					<van-grid-item use-slot v-for="(goods,i) in goodsList" :key="i" url="../pagesGoods/goods/index"
						@click="saveGoodsID(goods.goodsId)">

						<view class="goods">
							<!-- 商品图片 -->
							<image style="width: 320rpx; height: 300rpx;" :src="goods.goodsImg" />
							<!-- 商品名字 -->
							<view class="goodsNameText">
								<text class="goodsName gname">{{goods.goodsName}}</text>
							</view>
							<!-- 商品价格 -->
							<view class="goodsPriceText">
								<text class="goodsCPrice">￥{{goods.goodsCPrice}}</text>
							</view>
							<!-- 商品介绍 -->
							<view class="goodsIntroductionText">
								<text class="goodsIntroduction">
									<text class="introduction">{{goods.goodsIntroductionText}}</text>
								</text>
							</view>

						</view>
					</van-grid-item>
				</van-grid>
			</view>
		</view>
	</view>
</template>
<script>
	export default {
		data() {
			return {
				// 下拉菜单栏
				dropdownMenus: [{
						text: '默认排序',
						value: 0
					},
					{
						text: '最多销量',
						value: 1
					},
					{
						text: '最多好评',
						value: 2
					},
				],
				//通告文字
				text: '商城开业大吉，所有商品折扣销售，热门商品8折起',
				dropdownValue: 0,
				searchValue: '',
				//轮播图配置
				lunboData: [],
				autoplay: true, //是否自动切换
				interval: 3000, //自动切换时长
				duration: 1200, //滑动动画时长
				circular: true, //是否自动采用衔接滑动
				//中部导航图
				icon: [],
				//商品实例图
				goodsList: [],
				
				searchType: 0,
				urls:[
					'http://47.98.134.114:8081/goodsItem/findAllByGnameLike',
					'http://47.98.134.114:8081/goodsItem/findSortBySales',
					'http://47.98.134.114:8081/goodsItem/findSortByFavorRate'
				]
			}
		},
		onLoad() {
			//轮播图
			this.swiperPictures();
			//中部导航栏tabBar
			this.tabBarIcon();
			//商品示例

			uni.request({
				url: 'http://47.98.134.114:8081/goodsItem/findTopSix',
				method: 'GET',
				data: {
					token: 1

				},
				success: res => {
					console.log(res, '打印Res')

					if (res.data.code == 0) {
						uni.showToast({
							title: '数据读取成功',
							duration: 2000,
							icon: 'none'
						})
						console.log(res.data.data)
						this.goodsList = []
						res.data.data.forEach((item) => {
							let data = {
								goodsId: item.sid,
								goodsName: item.gname,
								goodsCPrice: item.lowestprice,
								goodsIntroductionText: item.details,
								goodsImg: item.spic
							}
							this.goodsList.push(data)
						})
					}
				},
				fail: () => {

				},
				complete: () => {}
			});
		},
		methods: {
			/* 下拉跳转 */
			toGoodsList(e) {
				let dropdownValue = '';
				for (var i = 0; i < this.dropdownMenus.length; i++) {
					if (this.dropdownMenus[i].value == e.detail) {
						dropdownValue = this.dropdownMenus[i].text;
					}
				}
				this.searchType = e.detail
			},
			/* 搜索*/
			onChange(e) {
				// console.log(this.searchType, '呵呵');
				let searchData = '';
				searchData = e.detail; //e.detail是当前输入值
				
				uni.request({
					url: this.urls[this.searchType],
					method: 'GET',
					data: {
						name: searchData
					},
					success: res => {
						console.log(res.data.data, '打印Res');
						uni.setStorageSync('searchResult', res.data.data)
						uni.navigateTo({
							url: '/pages/searchpage/index'
						})
					}
				});
				

			},
			saveGoodsID(goodsId) {
				uni.setStorageSync('goodsItemId', goodsId)
				console.log(goodsId, '我喜欢这个世界!')
			},
			/* 取消onSearch */
			searchCancel() {
				console.log("取消搜索")

			},
			
			/* 轮播图 */
			swiperPictures() {
				var data = {
					"datas": [{
							"id": 1,
							"imgurl": "../../static/swiper/1.jpg",
						},
						{
							"id": 2,
							"imgurl": "../../static/swiper/2.jpg"
						},
						{
							"id": 3,
							"imgurl": "../../static/swiper/3.jpg"
						},
					]
				};
				this.lunboData = data.datas
			},
			/* 中部导航栏  新品、收藏、精品、推荐的页面跳转，图标以及文字*/
			tabBarIcon() {
				var data = {
					"icons": [{
							"photoSrc": "/static/icon/xinpin.png",
							"text": "新品",
							"toUrl": "/pages/shop/index"
						},
						{
							"photoSrc": "/static/icon/shoucang.png",
							"text": "收藏",
							"toUrl": "/pages/favorite/index"
						},
						{
							"photoSrc": "/static/icon/jingpin.png",
							"text": "精品",
							"toUrl": "/pages/searchpage/index"
						},
						{
							"photoSrc": "/static/icon/tuijian.png",
							"text": "推荐",
							"toUrl": "/pages/pagesGoods/goods/index"
						}
					]
				};
				this.icon = data.icons
			},
			/* 展示商品列表 */

			/* 限时秒杀跳转事件 */
			seckill(e) {
				uni.navigateTo({
					url: '/pages/pagesGoods/goods/index',
				})
			},
			/* 优惠券专区和套餐礼包跳转事件 */
			giftPack(e) {
				uni.navigateTo({
					url: '/pages/pagesGoods/goods/index',
				})
			},
		}
	}
</script>

<style>
	.content {
		width: 100%;
		height: 100%;
		margin: 0 auto;
	}

	.header {
		width: 100%;
		height: 20%;
	}

	.header .dropdown {
		width: 25%;
		height: 100%;
		float: left;
	}

	.header .search {
		width: 100%;
		height: 100%;
	}

	/*轮播控件*/
	.home-swiper {
		width: 98%;
		margin: auto;
		height: 360rpx;
	}

	.slide-image {
		width: 100%;
		height: 100%;
	}

	.arrondi {
		display: flex;
		justify-content: space-between;
		align-items: center;
		padding: 20rpx 20rpx;
	}

	.xsms image {
		width: 340rpx;
		height: 280rpx;
	}

	.xsmsRight image {
		width: 360rpx;
		height: 135rpx;
	}

	.title {
		width: 750rpx;

		display: flex;
		justify-content: center;
		align-items: center;
	}

	.title image {
		height: 200rpx;
		width: 200rpx;
		padding: 55rpx 0 30rpx 0;
	}

	.van-grid-item__content {
		padding: 6px 6px !important;
	}

	.goods {
		box-sizing: border-box;
		overflow: hidden;
		break-inside: avoid;
		border: 1px solid #efefef;
		border-radius: 5rpx;
		width: 100%;

	}

	.goodsNameText {
		width: 100%;
		height: 60%;
	}

	.goodsName {
		font-size: 30rpx;
		line-height: 40rpx;
		padding: 15rpx 15rpx 5rpx 15rpx;
		color: #555;
	}

	.gname {
		display: -webkit-box;
		word-break: break-all;
		text-overflow: ellipsis;
		overflow: hidden;
		-webkit-box-orient: vertical;
		-webkit-line-clamp: 1;
	}

	/* 设置最大显示行数 */

	.goodsPriceText {
		padding: 0 15rpx;
	}

	.goodsCPrice {
		font-size: 28rpx;
		color: firebrick;
		font-weight: bold;
		margin-right: 5rpx
	}



	.goodsIntroductionText {
		padding: 10rpx 15rpx;
		border-top: 1px solid #efefef;
	}

	.goodsIntroduction {
		font-size: 22rpx;
		line-height: 35rpx;
		color: #999;
		height: 60rpx;
	}

	.introduction {
		display: -webkit-box;
		word-break: break-all;
		text-overflow: ellipsis;
		overflow: hidden;
		-webkit-box-orient: vertical;
		-webkit-line-clamp: 1
	}
</style>
