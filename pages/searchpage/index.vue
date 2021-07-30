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
				<van-search :value="searchValue" shape="round" placeholder="请输入搜索关键词" show-action @search="onChange"
					@cancel="searchCancel" />
			</view>
		</view>
		<view class="contentMain">
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
				dropdownValue: 0,
				searchValue: '',
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
			console.log(uni.getStorageSync('searchResult'));
			this.goodsList = []
			uni.getStorageSync('searchResult').forEach((item) => {
				let data = {
					goodsId: item.sid,
					goodsName: item.gname,
					goodsCPrice: item.lowestprice,
					goodsIntroductionText: item.details,
					goodsImg: item.spic
				}
				this.goodsList.push(data)
			})
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
				let searchData = '';
				searchData = e.detail; //e.detail是当前输入值
				console.log(searchData, );
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
			/* 取消onSearch */
			searchCancel() {
				console.log("取消搜索")
				
				},
				saveGoodsID(goodsId) {
					uni.setStorageSync('goodsItemId', goodsId)
					console.log(goodsId, '我喜欢这个世界!')
				},
			},
			
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
