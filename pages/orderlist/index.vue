<template>
    <view class="orderTabs">
        <!-- 设置全部订单头部导航栏
			sticky开启粘性布局
			swipeable开启滑动切换标签页
		-->

        <van-tabs sticky @click="orderTabs" :active="active">
            <van-tab v-for="item in navArr" :title="item.title" :name="item.name" :key="item.name">
                <view class="navTabs">
                    <van-swipe-cell v-for="(datas,index) in showDatas" :key="datas.orderId">
                        <view class="chengboxs">
                        </view>
                        <van-card class="goods-card" :desc="'订单号:'+datas.orderId" :num="datas.orderNum"
                            :price="formatPrice(datas.orderPrice)" :title="datas.orderTitle" :thumb="datas.thumb">
                            <view slot="tags">
                                <van-tag class="vantags" v-if="tagsShow" size="medium" :type="datas.orderType">
                                    {{datas.orderStateText}}
                                </van-tag>
                            </view>
                        </van-card>
                    </van-swipe-cell>
                </view>
            </van-tab>
        </van-tabs>
    </view>
</template>

<script>
    export default {
        data() {
            return {
                active: 'all', //默认的显示状态
                showDatas: [], //需要显示的数据
                tagsShow: true,
                navArr: [{
                        title: '全部订单',
                        name: 'all'
                    },
                    {
                        title: '待付款',
                        name: 'dfk'
                    },
                    {
                        title: '待发货',
                        name: 'dfh'
                    },
                    {
                        title: '待收货',
                        name: 'dsh'
                    },
                    {
                        title: '已完成',
                        name: 'ywc'
                    },

                ],
                navDatas: [{
                    orderId: 15,
                    orderState: 'dfk',
                    orderStateText: '未支付',
                    orderType: 'primary',
                    orderTitle: '三只松鼠大礼包',
                    orderPrice: 19.00,
                    orderNum: 1,
                    thumb: 'https://img14.360buyimg.com/n0/jfs/t1/183340/28/4073/373127/609dd9ffEd7bcadf9/7e5107ff3a5d3102.jpg'
                }, ],
            }
        },
        methods: {
            //标签切换
            orderTabs(e) {
                var that = this;
                that.showDatas = [];
                console.log(e.detail);
                for (var i = 0; i < that.navDatas.length; i++) {
                    if (e.detail.name == that.navDatas[i].orderState) {
                        that.showDatas.push(that.navDatas[i])
                    } else if (e.detail.name == 'all') {
                        that.showDatas = that.navDatas
                    }
                }
                console.log(that.showDatas);
            },
            //计算价格
            formatPrice(price) {
                return price.toFixed(2);
            },
        },
        onLoad: function(option) {
            uni.request({
                    url: 'http://47.98.134.114:8081/order/findAllOrderByUid',
                    method: 'GET',
                    data: {
                        uid: uni.getStorageSync('user').uid
                    },
                    success: res => {
                        console.log('成功');
                        res.data.data.forEach(item => {
                            var info = {
                                    orderId: item.orderId,
                                    orderState: '',
                                    orderStateText: item.orderStatus,
                                    orderType: 'primary',
                                    orderTitle: '',
                                    orderPrice: item.totalPrice,
                                    orderNum: item.goodsCount,
                                    thumb: '',
                                }
                            uni.request({
                                url: 'http://47.98.134.114:8081/goodsItem/findBySid',
                                method: 'GET',
                                data:{
                                    sid: item.sid
                                },
                                success: goodsRes=>{
                                    info.orderTitle = goodsRes.data.data.gname
                                    info.thumb = goodsRes.data.data.spic
                                    if(info.orderStateText == '未支付')
                                        info.orderState = 'dfk'
                                        else if (info.orderStateText == '待收货')
                                        info.orderState = 'dsh'
                                        else if(info.orderStateText == '待发货')
                                        info.orderState = 'dfh'
                                        else if(info.orderStateText == '已完成')
                                        info.orderState = 'ywc'
                                    this.navDatas.push(info)
                                }
                            })
                            
                                
                            })
                        }
                })
                this.active = option.active
                this.showDatas = [];
                for (var i = 0; i < this.navDatas.length; i++) {
                    if (option.active == this.navDatas[i].orderState) {
                        this.showDatas.push(this.navDatas[i])
                    } else if (option.active == 'all') {
                        this.showDatas = this.navDatas
                    }
       
            }
        }
    }
    
</script>

<style>
    .navTabs {
        width: 100%;
        height: 100%;
        margin-bottom: 5%;
    }

    .navTabs .goods-card {
        margin-bottom: 20rpx;
    }

    .vantags {
        position: absolute;
        top: -2rpx;
        right: -20rpx;
    }

    .chengboxs {
        margin-top: 10rpx;
    }
</style>
