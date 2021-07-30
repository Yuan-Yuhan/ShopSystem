<template>
    <view class="container">
        <!-- 商品轮播图 -->
        <view class="carousel">
            <!--轮播图-->
            <swiper class="home-swiper" indicator-dots="true" :autoplay="autoplay" :interval="interval"
                :circular="circular" :duration="duration">
                <block v-for="(item,key) in lunboData" :key="key">
                    <swiper-item class="swiper-item">
                        <image :src="item.imgurl" class="slide-image" />
                    </swiper-item>
                </block>
            </swiper>
        </view>
        <!-- 商品介绍部分 -->
        <view class="introduce-section" :key="index" v-for="(goodsInf,index) in goodsInf">
            <text class="title">{{goodsInf.goodsTitle}}</text>
            <view class="price-box">
                <text class="price-tip">￥</text>
                <text class="price">{{specSelected[0].price}}</text>
                <text class="m-price" v-show="specSelected[0].show">￥{{specSelected[0].originalPrice}}</text>
                <text class="coupon-tip" v-show="specSelected[0].show">{{specSelected[0].discountOnShow}}折</text>
                <!--  折扣 -->
            </view>
            <view class="bot-row">
                <text>销量：{{goodsInf.goodsSales}}</text>
                <text>库存：{{specSelected[0].inventory}}</text>
                <text>好评率: {{favorableRate}}%</text>
            </view>
        </view>


        <!-- 详情页面显示的  -购买类型 -->
        <view class="c-list" @click="typeShowMethod">
            <van-cell is-link>
                <view slot="title">
                    <view class="van-cell-text">产品规格</view>
                    <view class="con">
                        <text class="select-text" v-for="(sItem, sIndex) in specSelected"
                            :key="sIndex">{{sItem.name}}</text>
                    </view>
                </view>
            </van-cell>
        </view>

        <!-- 弹出的 类型信息 -->
        <van-action-sheet @close="onClose" overlay close-on-click-overlay round :show="typeShow" :title="typeTitle">
            <view class="typeInformation">
                <view class="a-t">

                    <image :src="specSelected[0].gpic" role="img"></image>

                    <view class="right" :key="index" v-for="(goodsInf,index) in goodsInf">

                        <text class="price">￥{{specSelected[0].price}}</text>
                        <text class="stock">库存：{{specSelected[0].inventory}}件</text>
                        <view class="selected">
                            已选：
                            <text class="select-text" v-for="(sItem, sIndex) in specSelected"
                                :key="sIndex">{{sItem.name}}</text>
                        </view>

                    </view>

                </view>

                <view v-for="(item,index) in specList" :key="index" class="attr-list">
                    <text>{{item.name}}</text>
                    <view class="item-list">
                        <!-- 仅当规格不为空时显示出来 -->
                        <text v-for="(childItem, childIndex) in specChildList"
                            v-if="childItem.pid === item.id && childItem.name != ''" :key="childIndex" class="tit"
                            :class="{selected: childItem.selected}" @click="selectSpec(childIndex, childItem.pid)">
                            {{childItem.name}}
                        </text>
                    </view>
                </view>
                <view class="vButton">
                    <van-button plain round block color="#fa436a" type="primary" @click="onClose">完成</van-button>
                </view>
            </view>
        </van-action-sheet>

        <!-- 促销活动 -->
        <view class="c-row b-b">
            <text class="tit">促销活动</text>
            <view class="con-list">
                <text>订单满300减20</text>
                <text>订单满500减50</text>
            </view>
        </view>
        <!-- 服务 -->
        <view class="c-row b-b">
            <text class="tit">服务</text>
            <view class="bz-list con">
                <text>7天无理由退货 ·</text>
                <text>假一赔十 · </text>
            </view>
        </view>


        <!-- 评价头 -->
        <view class="eva-section">
            <view class="e-header">
                <van-cell is-link value="">
                    <view slot="title">
                        <view class="van-cell-text">评价({{rateCount}})</view>
                    </view>
                </van-cell>
            </view>

            <!-- 用户评价详情 -->
            <view class="eva-box" v-for="(comment, i) in commentList" :key="i">
                <van-image width="100%" height="100%" round class="myPhoto" src="/static/images/img/myphoto.png">
                </van-image>
                <view class="right">
                    <text class="name">{{comment.name}}</text>
                    <text class="con">{{comment.content}}</text>
                    <view class="bot">
                        <text class="attr">购买类型：XL 白色</text>
                        <text class="time">2021-07-10 19:21</text>
                    </view>
                </view>
            </view>
        </view>


        <!-- 图文详情 -->
        <view class="detail-desc">
            <view class="d-header">
                <text>———— 图文详情 ————</text>
            </view>
            <view class="d-photo">
                <van-image width="100%" height="100%" class="photo" v-for="(photo,i) in photoInformation"
                    :src="photo.photoSrc" :key="i">
                </van-image>
            </view>
        </view>


        <!-- 底部导航栏 -->
        <view class="page-bottom">
            <van-goods-action>
                <van-goods-action-icon icon="home-o" text="店铺" @click="goshop" />
                <van-goods-action-icon icon="cart-o" text="收藏" @click="addToFavorite" />
                <van-goods-action-button @click="addToShopCart" text="加入购物车" type="warning" />
                <van-goods-action-button @click="ImmediatePurchase" text="立即购买" />
            </van-goods-action>
        </view>

        <van-toast id="van-toast" /> <!-- 轻提示 -->
    </view>
</template>

<script>
    export default {
        data() {
            return {

                //评价数目
                rateCount: '',
                //好评率
                favorableRate: '',
                //商品详情展示图
                showIcons: [],
                //评论列表
                commentList: [{
                        avatar: '/static/icon/cart.png',
                        name: '小明',
                        content: '孩子很喜欢',
                        type: 'XL',
                        Date: '2019-09-08',
                    },
                    {
                        avatar: '/static/icon/cart.png',
                        name: '小明',
                        content: '孩子很喜欢',
                        type: 'XL',
                        Date: '2019-09-08',
                    },
                    {
                        avatar: '/static/icon/cart.png',
                        name: '小明',
                        content: '孩子很喜欢',
                        type: 'XL',
                        Date: '2019-09-08',
                    },
                ],

                //轮播图配置
                lunboData: [],
                autoplay: true, //是否自动切换
                interval: 3000, //自动切换时长
                duration: 1200, //滑动动画时长
                circular: true, //是否自动采用衔接滑动

                //后台传入的商品信息
                goodsItem: {},

                //商品信息
                goodsInf: [],

                //类型
                typeShow: false,
                typeTitle: '商品类型',

                specSelected: [],

                // 规格信息
                specList: [{
                    id: 1,
                    name: '规格类型',
                }, ],
                specChildList: [],
                //图表详情
                photoInformation: [],
            }
        }, //data
        onLoad() {
            uni.request({
                url:  'http://47.98.134.114:8081/address/getAddress',
                method: 'GET',
                data:{
                    uid: uni.getStorageSync('user').uid
                },
                success:res=>{
                    	uni.setStorageSync('addressInfo', res.data.data)
                }
            })
            uni.request({
                //请求商品信息
                url: 'http://47.98.134.114:8081/goodsItem/findBySid',
                method: 'GET',
                data: {
                    //传商品ID
                    sid: uni.getStorageSync('goodsItemId'),
                },
                //获取商品信息成功
                success: res => {
                    this.goodsItem = res.data.data
                    this.rateCount = this.goodsItem.rateCount
                    this.favorableRate = this.goodsItem.favorablerate * 100
                    var icon = {
                        photoSrc: this.goodsItem.spic,
                    }
                    this.photoInformation.push(icon)

                    //存储商户id
                    uni.setStorageSync('merchantId', res.data.data.merchantId)
                    //存储商铺信息
                    uni.request({
                        url: 'http://47.98.134.114:8081/merchant/findMerchant',
                        method: 'GET',
                        data: {
                            merchantId: uni.getStorageSync('merchantId')
                        },
                        success: res => {
                            console.log(res, '猪猪侠')

                            uni.setStorageSync('SHOP', res.data.data)

                        }

                    });

                    //获取规格信息
                    uni.request({
                        //请求规格信息
                        url: 'http://47.98.134.114:8081/goods/findAllBySid',
                        method: 'GET',
                        data: {
                            sid: uni.getStorageSync('goodsItemId'),
                        },
                        success: res => {
                            res.data.data.forEach((item) => {
                                let data = {
                                    id: item.gid, //gid规格ID
                                    pid: 1,
                                    name: item.specification, //specification规格
                                    discount: item.discount,
                                    discountOnShow: item.discount * 10,
                                    show: item.discount == 1 ? false : true,
                                    gpic: item.gpic,
                                    inventory: item.inventory,
                                    price: item.price,
                                    sid: item.sid,
                                    originalPrice: item.originalPrice,
                                }
                                var icon = {
                                    photoSrc: item.gpic,
                                }
                                //把数据赋值给photoInformation图文详情

                                this.photoInformation.push(icon)
                                this.specChildList.push(data)

                            })
                            //规格 默认选中第一条
                            this.specList.forEach(item => {
                                for (let cItem of this.specChildList) {
                                    if (cItem.pid === item.id) {
                                        this.$set(cItem, 'selected', true);
                                        this.specSelected.push(cItem);
                                        break; //forEach不能使用break
                                    }
                                }
                            });
                            //轮播图
                            this.swiperPictures();
                            //------------------商品信息/规格信息/介绍
                            var data = [{
                                goodsId: this.$data.goodsItem.sid,
                                goodsTitle: this.$data.goodsItem.gname, //标题
                                goodsPirce: this.$data.goodsItem.lowestprice, //现价
                                goodsMPrice: this.$data.goodsItem.lowestprice *
                                    1.25, //原价
                                goodsCoupon: "8折", //折扣
                                goodsSales: this.$data.goodsItem.sales, //销量
                                goodsInventory: 1483, //库存

                            }, ];
                            //把数值赋值给goodsInf商品信息介绍
                            this.goodsInf = data
                            //-------------------商品信息
                        }
                    })
                }
            });


        },
        methods: {
            //轮播图图片
            swiperPictures() {
                var data = {
                    "datas": [{
                            "id": 1,
                            "imgurl": this.goodsItem.spic
                        },
                        {
                            "id": 2,
                            "imgurl": this.specChildList[0].gpic
                        },
                        {
                            "id": 3,
                            "imgurl": this.specChildList[1].gpic
                        },
                    ]
                };
                this.lunboData = data.datas
            },

            //关闭弹窗
            onClose() {
                this.typeShow = false;
            },


            //加载类型弹窗
            typeShowMethod() {
                this.typeShow = true;
            },

            //选择规格
            selectSpec(index, pid) {
                let list = this.specChildList;
                list.forEach(item => {
                    if (item.pid === pid) {
                        this.$set(item, 'selected', false);
                    }
                })

                this.$set(list[index], 'selected', true);
                //存储已选择
                /**
                 * 修复选择规格存储错误
                 * 将这几行代码替换即可
                 * 选择的规格存放在specSelected中
                 */
                this.specSelected = [];
                list.forEach(item => {
                    if (item.selected === true) {
                        this.specSelected.push(item);
                    }
                })
            },

            //添加到商品收藏
            addToFavorite() {
                uni.request({
                    url: 'http://47.98.134.114:8081/favorite/add',
                    method: 'GET',
                    data: {
                        uid: uni.getStorageSync('user').uid,
                        sid: uni.getStorageSync('goodsItemId'),
                    },
                    success: res => {
                        if (res.data.code == 206) {
                            uni.showToast({
                                title: '请勿重复收藏',
                                duration: 2000,
                                icon: 'error'
                            })
                        } else if (res.data.code == 0) {
                            uni.showToast({
                                title: '收藏成功',
                                duration: 2000,
                                icon: 'success'
                            })
                        } else if (res.data.code == 201) {
                            uni.showToast({
                                title: '错误的用户ID',
                                duration: 2000,
                                icon: 'error'
                            })
                        } else {
                            uni.showToast({
                                title: '错误的商品ID',
                                duration: 2000,
                                icon: 'error'
                            })
                        }

                    }
                })
            },

            //加入购物车
            addToShopCart() {
                uni.request({
                    url: 'http://47.98.134.114:8081/cart/add',
                    method: 'GET',
                    data: {
                        uid: uni.getStorageSync('user').uid,
                        sid: uni.getStorageSync('goodsItemId'),
                        gid: this.specChildList[0].id,
                    },
                    success: res => {
                        if (res.data.code == 0) {
                            uni.showToast({
                                title: '加入购物车成功',
                                duration: 2000,
                                icon: 'success'
                            })
                        } else if (res.data.code == 909) {
                            uni.showToast({
                                title: res.data.msg,
                                duration: 2000,
                                icon: 'error'
                            })
                        } else if (res.data.code == 2) {
                            uni.showToast({
                                title: '用户不存在!',
                                duration: 2000,
                                icon: 'error'
                            })
                        } else if (res.data.code == 99) {
                            uni.showToast({
                                title: '商品不存在',
                                duration: 2000,
                                icon: 'error'
                            })
                        } else if (res.data.code == 88) {
                            uni.showToast({
                                title: '规格不存在',
                                duration: 2000,
                                icon: 'error'
                            })
                        }
                    }
                })
            },
            //立即购买
            ImmediatePurchase() {
                uni.setStorageSync('goodsItemForOrder', this.goodsItem) //用于订单的商品信息
                uni.setStorageSync('goodsForOrder', this.specSelected[0]) //用于订单的规格信息
				uni.navigateTo({
					url:'/pages/order/index'
				})

            },
            goshop() {
                uni.navigateTo({
                    url: '/pages/shop/index'
                })
            }
        }
    }
</script>

<style>
    page {
        background: #f8f8f8;
        padding-bottom: 160rpx;
    }

    .container {
        width: 100%;
        height: 100%;
        margin: 0 auto;
    }

    .carousel {
        height: 722rpx;
        position: relative
    }

    /*轮播控件*/
    .home-swiper {
        width: 100%;
        height: 100%;
    }

    .swiper-item {
        display: flex;
        justify-content: center;
        align-content: center;
        height: 750upx;
        overflow: hidden;
    }

    .slide-image {
        width: 100%;
        height: 100%;
    }

    /* 商品介绍 */
    .introduce-section {
        background: #ffffff;
        padding: 20rpx 30rpx;
    }

    .introduce-section .title {
        font-size: 32rpx;
        color: #303133;
        height: 50rpx;
        line-height: 50rpx;
    }

    .introduce-section .price-box {
        display: flex;
        align-items: baseline;
        height: 50rpx;
        padding: 10rpx 0;
        font-size: 26rpx;
        color: #fa436a;
    }

    .introduce-section .price {
        font-size: 34rpx;
    }

    .introduce-section .m-price {
        margin: 0 12rpx;
        color: #909399;
        text-decoration: line-through;
    }

    .introduce-section .coupon-tip {
        align-items: center;
        padding: 4rpx 10rpx;
        background: #fa436a;
        font-size: 24rpx;
        color: #ffffff;
        border-radius: 6rpx;
        line-height: 1;
        transform: translateY(-4rpx);
    }

    .introduce-section .bot-row {
        display: flex;
        align-items: center;
        height: 30rpx;
        font-size: 24rpx;
        color: #909399;
    }

    .introduce-section .bot-row text {
        flex: 1;
    }


    .c-list {
        width: 100%;
        font-size: 26rpx;
    }

    .c-list .van-cell-text {
        width: 25%;
        display: flex;
        float: left;
        color: #606266;
    }

    .c-list .con .select-text {
        margin-right: 10rpx;
        color: #303133;
    }

    .typeInformation {
        width: 100%;
        margin-bottom: 5%;
    }

    .typeInformation .a-t {
        display: flex;
        width: 90%;
        margin: 0 auto;
    }

    .typeInformation .a-t image {
        width: 130rpx;
        height: 130rpx;
        border-radius: 8rpx;
    }

    .typeInformation .a-t .right {
        display: flex;
        flex-direction: column;
        padding-left: 24rpx;
        font-size: 26rpx;
        color: #606266;
        line-height: 42rpx;
    }

    .typeInformation .a-t .right .price {
        font-size: 32rpx;
        color: #fa436a;
        margin-bottom: 10rpx;

    }

    .typeInformation .a-t .right .select-text {
        margin-right: 10rpx;
    }

    .typeInformation .attr-list {
        display: flex;
        flex-direction: column;
        font-size: 30rpx;
        color: #606266;
        padding-top: 30rpx;
        padding-left: 10rpx;
    }

    .typeInformation .item-list {
        padding: 20rpx 0 0;
        display: flex;
        flex-wrap: wrap;
    }

    .typeInformation .item-list .selected {
        background: #fbebee;
        color: #fa436a;
    }

    .typeInformation .item-list text {
        display: flex;
        -webkit-box-align: center;
        -webkit-align-items: center;
        align-items: center;
        -webkit-box-pack: center;
        -webkit-justify-content: center;
        justify-content: center;
        background: #eee;
        margin-right: 20rpx;
        margin-bottom: 20rpx;
        border-radius: 100rpx;
        min-width: 60rpx;
        height: 60rpx;
        padding: 0 20rpx;
        font-size: 28rpx;
        color: #303133;
    }

    .typeInformation .vButton {
        width: 90%;
        margin: 0 auto;
        margin-top: 30rpx
    }

    .c-row {
        display: flex;
        align-items: center;
        padding: 20rpx 30rpx;
        position: relative;
        font-size: 26rpx;
        color: #606266;
        background-color: #FFFFFF;
    }

    .c-row .tit {
        width: 23%;
    }

    .c-row .con-list {
        flex: 1;
        display: flex;
        flex-direction: column;
        color: #303133;
        line-height: 40rpx;
    }

    .c-row .bz-list {
        height: 40rpx;
        font-size: 26rpx;
        color: #303133;
    }

    .c-row .con {
        flex: 1;
    }

    .c-row .bz-list text {
        display: inline-block;
        margin-right: 30rpx;
    }

    .eva-section {
        background-color: #FFFFFF;
        display: flex;
        flex-direction: column;
        margin-top: 16rpx;
    }

    .eva-section .eva-box {
        display: flex;
        padding: 20rpx 32rpx;
    }

    .eva-section .eva-box .myPhoto {
        flex-shrink: 0;
        width: 80rpx;
        height: 80rpx;
    }

    .eva-box .right {
        flex: 1;
        display: flex;
        flex-direction: column;
        font-size: 28rpx;
        color: #606266;
        padding-left: 26rpx;
    }

    .eva-box .right .con {
        font-size: 28rpx;
        color: #303133;
        padding: 20rpx 0;
    }

    .eva-box .right .bot {
        font-size: 24rpx;
        display: flex;
        color: #909399;
    }

    .detail-desc {
        background-color: #FFFFFF;
        margin: 16rpx 0px;
        height: 2026px;
    }

    .detail-desc .d-header {
        align-items: center;
        height: 80rpx;
        font-size: 30rpx;
        color: #303133;
        position: relative;
        text-align: center;
        line-height: 80rpx;

    }

    .detail-desc .d-header text {
        padding: 0 20rpx;
        background: #FFFFFF;
        position: relative;
    }

    .detail-desc .d-photo {
        width: 100%;
        height: 300px;
    }

    .page-bottom {
        position: fixed;
        left: 30rpx;
        bottom: 30rpx;
        display: flex;
        width: 690rpx;
        height: 100rpx;
        border-radius: 16rpx;

    }
</style>
