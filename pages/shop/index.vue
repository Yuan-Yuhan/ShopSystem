<template>
	<view id="qbsp" class="content">
		<view class="header">
			<view class="search">
				<!-- 搜索框 -->
				<van-search :value="searchValue" shape="round" placeholder="请输入搜索关键词" show-action @search="onChange"
					@cancel="searchCancel" />
			</view>
			<view class="eva-box">
				<van-image width="100%" height="100%" round class="myPhoto" v-bind:src="photoSrc">
				</van-image>
				<view class="right">
					<text class="name">{{shopname}}</text>
					<van-button type="danger" size='mini' @click="becomeHuman">成为会员</van-button>
				</view>
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

			<!-- 人气商品商品列表 -->
			<view class="goodsList">
				<van-grid :column-num="2" :border="false">
					<!-- 列数和间距 -->
					<van-grid-item use-slot v-for="(goods,i) in goodsList" :key="i" url="../pagesGoods/goods/index"
					@click="saveGoodsID(goods.goodsId)">
						<view class="goods">
							<image style="width: 320rpx; height: 300rpx;" :src="goods.goodsImg" />

							<view class="goodsNameText">
								<text class="goodsName gname">{{goods.goodsName}}</text>
							</view>
							<view class="goodsPriceText">
								<text class="goodsCPrice">￥{{goods.goodsCPrice}}</text>
							</view>
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
		<van-goods-action>
			<van-goods-action-icon icon="shop" text="首页" bind:click="onClickIcon" />
			<van-goods-action-icon icon="cart-o" text="商品" bind:click="onClickIcon" />
			<van-goods-action-icon icon="gem-o" text="优惠券" bind:click="onClickIcon" />
			<van-goods-action-icon icon="gift-o" text="秒杀" bind:click="onClickIcon" />
			<van-goods-action-icon icon="chat-o" text="客服" bind:click="onClickIcon" />
		</van-goods-action>
	</view>
</template>
<script>
	export default {
		data() {
			return {
				//通告文字
				text: '本店新开,全场商品六折起,更有大额优惠券和限时秒杀活动，欢迎抢购!',
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
				shopname: uni.getStorageSync('SHOP').merchantName,
				photoSrc: uni.getStorageSync('SHOP').merchantPic,
			}
		},
		onLoad() {
			//轮播图
			this.swiperPictures();
			
			uni.request({
				url: 'http://47.98.134.114:8081/goodsItem/findAllGoodsItem',
				method: 'GET',
				data: {
					merchantId: uni.getStorageSync('merchantId')
				},
				success: res => {
					console.log(res.data.data, '喜羊羊')
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
					uni.setStorageSync('SHOPGOODS', res.data.data)
				}
			
			});
			




		},
		methods: {
            becomeHuman(){
                uni.request({
                    url: 'http://47.98.134.114:8081/vip/becomeVIP',
                    method:'GET',
                    data:{
                        uid: uni.getStorageSync('user').uid,
                        merchantId: uni.getStorageSync('SHOP').merchantId
                    },
                    success: res=>{
                        if(res.data.code == 806)
                        {
                            uni.showToast({
                                icon: 'error',
                                title: res.data.msg,
                                duration: 2000
                            })
                        }else if (res.data.code == 0)
                        {
                            uni.showToast({
                                icon: 'success',
                                title: '已完成',
                                duration:2000
                            })
                        }
                    }
                })
            },
			saveGoodsID(goodsId) {
				uni.setStorageSync('goodsItemId', goodsId)
				
			},
			/* 搜索*/
			onChange(e) {
				let searchData = '';
				searchData = e.detail; //e.detail是当前输入值
				console.log(searchData);

			},

			/* 取消搜索 */
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

	.goodsPriceText {
		padding: 0 15rpx;
	}

	.goodsCPrice {
		font-size: 28rpx;
		color: firebrick;
		font-weight: bold;
		margin-right: 5rpx
	}

	.goodsOPrice {
		font-size: 30rpx;
		color: #aaa;
		text-decoration: line-through;
		padding-bottom: 10rpx
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

	.eva-box {
		display: flex;
		padding: 20rpx 32rpx;
	}

	.eva-box .myPhoto {
		flex-shrink: 0;
		width: 200rpx;
		height: 200rpx;
	}

	.eva-box .right {
		flex: 1;
		display: flex;
		flex-direction: column;
		font-size: 80rpx;
		color: #000000;
		padding-left: 26rpx;
	}
</style>
