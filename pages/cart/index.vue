<template>
    <view class="catContent">
        <!-- 商品卡片栏 -->
        <!-- 一次只能选一个商品添加订单 -->
        <view class="cards">
            <view class="checkedStyle">
                <van-swipe-cell :right-width="65" :name="item.gname" :key="index" v-for="(item,index) in cardGoodsList">
                    <view class="chengboxs">
                        <van-checkbox-group @change="onCardChange" :value="checkBox" max=1>
                            <van-checkbox :name="item.id" :key="item.id">
                            </van-checkbox>
                        </van-checkbox-group>
                    </view>
                    <van-card :num="item.num" :origin-price="formatPrice(item.oprice)" :price="formatPrice(item.price)"
                        :desc="item.desc" :title="item.title" class="goods-card" :thumb="item.thumb">
                        <view slot="footer">
                            <van-button :key="item" @click="numReduce(item.id)" size="mini">-</van-button>
                            <van-button :key="item" @click="numIncrease(item.id)" size="mini">+</van-button>
                        </view>
                    </van-card>
                    <van-button slot="right" @click="cardDelete(item.id, index)" type="danger" class="delete-button">删除
                    </van-button>
                </van-swipe-cell>
            </view>
            <!-- 提交订单栏 -->
            <view class="submitBar">
                <van-submit-bar  button-text="提交订单" @submit="onClickButton" :tip="nihao" :disabled="flag">
                </van-submit-bar>
            </view>
        </view>
    </view>
</template>

<script>
    export default {
        data() {
            return {
                checked: false,
                cardChecked: true,
                //CheckBox的数量
                checkBoxs: [],
                //CheckBox的状态, true或false
                checkBox: [],
                //商品列表
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
            //增加数量
            numIncrease(id) {
                console.log("增加数量")
                for (var i = 0; i < this.cardGoodsList.length; i++) {
                    if (this.cardGoodsList[i].id == id) {
                        this.cardGoodsList[i].num++;
                    }
                }
            },
            //减少数量
            numReduce(id) {
                console.log("减少数量")
                for (var i = 0; i < this.cardGoodsList.length; i++) {
                    if (this.cardGoodsList[i].id == id && this.cardGoodsList[i].num > 0) {
                        this.cardGoodsList[i].num--;
                    }
                }
            },
            //删除商品事件
            cardDelete(id, index) {
                uni.request({
                    url: 'http://47.98.134.114:8081/cart/delete',
                    method:'GET',
                    data:{
                        cartId: id,
                    },
                    success: res=>{
                        console.log(res.data);
                        uni.showToast({
                            title: '删除成功',
                            duration: 2000,
                            icon: 'success'
                        })
                    }
                })
                this.cardGoodsList.splice(index, 1);
            },
            //提交事件
            onClickButton() {
                    uni.navigateTo({
                        url : '/pages/order/index'
                    })
            },
            //商品单选事件
            onCardChange(event) {
                this.selectedCartId = event.detail[0]
                if(this.selectedCartId != null)
                {
                    uni.request({
                        url: 'http://47.98.134.114:8081/cart/findCartByCartId',
                        method:'GET',
                        data: {
                            cartId: this.selectedCartId
                        },
                        success: res=>{
                            uni.request({
                                url: 'http://47.98.134.114:8081/goodsItem/findBySid',
                                method:'GET',
                                data:{
                                    sid: res.data.data.sid
                                },
                                success: aResult=>{
                                    uni.setStorageSync('goodsItemForOrder', aResult.data.data)
                                }
                            })
                            uni.request({
                                url: 'http://47.98.134.114:8081/goods/findByGid',
                                method:'GET',
                                data: {
                                    gid: res.data.data.gid
                                },
                                success: bResult=>{
                                    uni.setStorageSync('goodsForOrder', bResult.data.data)
                                }
                            })
                            
                        }
                    })
                    this.flag = false
                }else
                {
                    this.flag = true
                }
                this.checkBox = event.detail
            },
            
            // 获取缓存store中购物车数据
                getGoodsList() {
                uni.request({
                    url: 'http://47.98.134.114:8081/cart/findUserCart',
                    method: 'GET',
                    data: {
                        uid: uni.getStorageSync('user').uid,
                    },
                    success: res=>{
						console.log(res,'破防了');
                        //得到购物车列表
                        
                        //发起请求商品名
                        res.data.data.forEach(item=>{
                             uni.request({
                                url: 'http://47.98.134.114:8081/goods/findByGid',
                                method: 'GET',
                                data:{
                                    gid: item.gid
                                },
                                success: res=>{
                                    var cartItemData =
                                    {
                                        id: item.cartId,
                                        title: item.gname,
                                        desc: res.data.data.specification,
                                        price: res.data.data.price,
                                        oprice: res.data.data.originalPrice,
                                        num: 1,
                                        thumb: res.data.data.gpic
                                    }
                                    
                                    this.cardGoodsList.push(cartItemData)
                                    this.checkBoxs.push(String(cartItemData.id))
                                }
                            })//请求规格中断
                            
                        }) // foreach中断
                        
                        
                    } //success中断
                })//购物车uni.request中断
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
