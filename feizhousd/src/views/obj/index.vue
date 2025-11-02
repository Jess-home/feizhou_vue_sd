<template>
    <div class="obj home">
        <div class="img_loading" v-if="loading">
            <img :src="loadImg" class="img" alt="">
            <div>{{loadText}}</div>
        </div>
        <van-nav-bar @click-right="clickRight">
            <template #right>
                <img :src="require('@/assets/images/self/hank/tel.png')" class="img" height="25" alt="">
            </template>
        </van-nav-bar>
        <div class="content">
            <div class="money">
                <div class="left">
                    <div class="top">{{currency}}
                        <span class="span">{{info.uinfo?.balance_format}}</span>
                    </div>
                    <div class="text">{{$t('msg.my_yu_e')}}</div>
                </div>
                <div class="right">
                    <!-- <img :src="require('@/assets/images/order/add.png')" class="img" alt=""> -->
                    <van-button icon="plus" type="primary" to="chongzhi"/>
                </div>
            </div>
            <div class="vip">
                <!-- <img :src="require('@/assets/images/order/info.png')" alt="" class="right" @click="level_show=true"> -->
                <div class="top">
                    <!-- <img :src="require('@/assets/images/home/huiyuan.png')" class="l" alt=""> -->
                    <div class="r">
                        <div class="title">{{info.level_info?.name || ''}}</div>
                        <div class="sub">
                            {{$t('msg.zdye')}}：{{info.level_info?.num_min || 0.00}}
                        </div>
                        <div class="sub s1">
                            {{$t('msg.yonj2')}}：{{(info.level_info?.bili*100 || 0).toFixed(1)}}%
                        </div>
                    </div>
                </div>
                <div class="b">
					<!-- 已抢单数 -->
                    <!-- <span class="span">{{info.day_d_count || 0}}</span>/{{info.order_num || 0}} -->
                    <!-- 已完成单数 -->
					<span class="span">{{info.day_completed_count || 0}}</span>/{{info.order_num || 0}}
                </div>
            </div>
            <div class="yonj">
                <div class="li">
                    <div class="top">{{currency}}{{info.day_deal || 0.00}}</div>
                    <div class="text">{{$t('msg.yonj2')}}</div>
                </div>
                <div class="li">
                    <div class="top">{{currency}}{{info.lock_deal || 0.00}}</div>
                    <div class="text">{{$t('msg.djje')}}</div>
                </div>
                <div class="li">
                </div>
                <div class="li">
                    <div class="top">{{currency}}{{info.uinfo?.balance_format || 0.00}}</div>
                    <div class="text">{{$t('msg.kyye')}}</div>
                </div>
                <div class="btnn">
                    <van-button round block type="primary" class="ksqg" @click="getDd()">
                        <img :src="require('@/assets/images/news/click.png')" class="img" height="35" alt="">
                        {{loading ? $t('msg.hddd') : $t('msg.ksqg')}}
                    </van-button>
                </div>
            </div>
            <div class="qd">
                <!-- <div class="title">{{$t('msg.qdsm')}}</div> -->
                <div class="sub" v-html="content"></div>
            </div>
        </div>

        <!-- 会员等级弹窗 -->
        <van-dialog v-model:show="level_show" :title="$t('msg.djsm')" :cancelButtonText="$t('msg.quxiao')" show-cancel-button :showConfirmButton="false">
            <!-- banner图 -->
            <van-swipe class="my-swipe" indicator-color="white">
                <van-swipe-item v-for="item in info.level_list || []" :key="item.id">
                    <div class="hy_box">
                        <div class="t">
                            <img :src="require('@/assets/images/home/huiyuan.png')" class="img" alt="">
                            <span class="text">{{$t('msg.hy_level')}} {{item.name}}</span>
                        </div>
                        <div class="b">
                            <div class="sub">{{$t('msg.sxtz')}}： {{currency}}{{item.num}}</div>
                            <div class="sub">{{$t('msg.yonj')}}： {{item.bili*100}}% <span class="line">|</span>{{item.order_num}}{{$t('msg.order')}}</div>
                        </div>
                        <van-button type="primary" class="txlevel" round block>{{item.level <= info.uinfo?.level ? $t('msg.now_level') : $t('msg.add_level')}}</van-button>
                    </div>
                </van-swipe-item>
            </van-swipe>
        </van-dialog>

        <van-dialog v-model:show="showTj" :confirmButtonText="$t('msg.queren')" :cancelButtonText="$t('msg.close')" :show-confirm-button="true" :title="$t('msg.ddxq')" show-cancel-button @confirm="confirmPwd">
            <template #title>
                <div style="text-align: center">{{$t('msg.ddxq')}}</div>
            </template>
            <div class="list" v-if="onceinfo.data?.group_rule_num == 0 || onceinfo.data?.duorw == 0">
                <div class="cet">
                    <img :src="onceinfo.data?.goods_pic" class="img" alt="">
                </div>
                <div class="monney">
                    <div class="tent">
                        <span class="span">{{$t('msg.ddh')}}</span>
                        <span class="value">{{onceinfo.data?.oid}}</span>
                    </div>
                    <div class="tent">
                        <span class="span">{{$t('msg.xdsj')}}</span>
                        <span class="value">{{formatTime('',onceinfo.data?.addtime)}}</span>
                    </div>
                    <div class="tent">
                        <span class="span">{{$t('msg.spdj')}}</span>
                        <span class="value">{{currency+onceinfo.data?.goods_price}}</span>
                    </div>
                    <div class="tent">
                        <span class="span">{{$t('msg.spsl')}}</span>
                        <span class="value">{{'x ' + onceinfo.data?.goods_count}}</span>
                    </div>
                    <div class="tent">
                        <span class="span">{{$t('msg.order_Num')}}</span>
                        <span class="value">{{currency+onceinfo.data?.num}}</span>
                    </div>
                    <div class="tent">
                        <span class="span">{{$t('msg.yonj')}}</span>
                        <span class="value">{{currency+onceinfo.data?.commission}}</span>
                    </div>
                </div>
            </div>
            <div class="list" v-else>
                <div class="tops">
                    <span class="span">{{$t('msg.ddrws')}}：</span>
                    <span class="span" style="color:red;">{{onceinfo.data?.duorw}}</span>
                </div>
                <div class="tops">
                    <span class="span">{{$t('msg.ywc')}}：</span>
                    <span class="span" style="color:#00a300;">{{onceinfo.data?.completedquantity}}</span>
                </div>
                <div class="box" v-for="item in onceinfo.group_data" :key="item.id">
                    <div class="cet">
                        <img :src="item?.goods_pic" class="img" alt="">
                    </div>
                    <div class="monney">
                        <div class="tent">
                            <span class="span">{{$t('msg.spdj')}}</span>
                            <span class="value">{{currency+item?.goods_price}}</span>
                        </div>
                        <div class="tent">
                            <span class="span">{{$t('msg.spsl')}}</span>
                            <span class="value">{{'x ' + item?.goods_count}}</span>
                        </div>
                        <div class="tent">
                            <span class="span">{{$t('msg.order_Num')}}</span>
                            <span class="value">{{currency+item?.num}}</span>
                        </div>
                        <div class="tent">
                            <span class="span">{{$t('msg.fkzt')}}</span>
                            <span class="value" :class="'value'+item.is_pay">{{item.is_pay === 0 ? $t('msg.dfk') : $t('msg.yfk')}}</span>
                        </div>
                    </div>
                </div>
            </div>
        </van-dialog>
    </div>
</template>
<script>
import { ref,getCurrentInstance, reactive} from 'vue';
import {rot_order,submit_order,order_info,do_order} from '@/api/order/index'
import store from '@/store/index'
import {getdetailbyid} from '@/api/home/index.js'
import {formatTime} from '@/api/format.js'
import { useRouter } from 'vue-router';
import { useI18n } from 'vue-i18n'
import {getsupport} from '@/api/tel/index'
export default {
    setup(){
         const { t } = useI18n()
        const { push } = useRouter();
        const {proxy} = getCurrentInstance()
        const level_show = ref(false)
        const loading = ref(false)
        const loadText = ref('')
        const loadImg = ref('')
        const currency = ref(store.state.baseInfo?.currency)
        const info = ref(store.state.objInfo)
        const onceinfo = ref({})
        const showTj = ref(false)
        const content = ref('')
        const support = ref('')
        
        const status_list= reactive([
            // {label: t('msg.dsh'),value: -1},
            {label: t('msg.dtj'),value: 0},
            {label: t('msg.ytj'),value: 1},
            {label: t('msg.yhqx'),value: 2},
            {label: t('msg.qzwc'),value: 3},
            {label: t('msg.qzqx'),value: 4},
            {label: t('msg.djz'),value: 5},
        ])
        store.dispatch('changefooCheck','obj')
        getdetailbyid(20).then(res => {
            content.value = res.data?.content
        })
        const initData = () => {
            rot_order().then(res => {
                if(res.code === 0) {
                    console.log(res)
                    info.value = {...res.data}
                    store.dispatch('changeobjInfo',info.value)
                }
            })
        }
        initData()
        const tjOrder = (row) => {
            order_info({id: row.oid}).then(res => {
                loading.value = false
                onceinfo.value = {...res}
                showTj.value = true
            })
        }

        const confirmPwd = () => {
            let id = ''
            if (onceinfo.value.group_data && onceinfo.value.group_data.length > 0) {
                let info = onceinfo.value.group_data?.find(rr => rr.is_pay === 0)
                id = info?.oid
            } else {
                id = onceinfo.value.data?.oid || onceinfo.value.data?.id
            }
            let json = {
                oid: id,
                status: 1
            }
            do_order(json).then(res => {
                if(res.code === 0) {
                    // data不存在或者duorw为0，不匹配订单
                    const group_data = onceinfo.value.group_data || []
                    if ((!onceinfo.value.data || onceinfo.value.data.duorw === 0)) {
                        proxy.$Message({ type: 'success', message:res.info});
                        showTj.value = false
                        initData()
                    } else if (group_data.length == onceinfo.value.data.duorw) {
                        //duorw > 0 但是 grpoup_data.length == duorw的情况，不进行匹配订单
                        proxy.$Message({ type: 'success', message:res.info});
                        showTj.value = false
                        initData()
                    } else {
                        submit_order().then(()=>{
                            showTj.value = false
                            Toast.success(t('msg.tjcg'))
                            initData()
                        })
                    }
                } else {
                    proxy.$Message({message: res.info, type: 'error'})
                }
            })
        }
        const cancelPwd = () => {
            initData()
        }
        const clickRight = () => {
            // push('/tel')
            if (support.value) {
                location.href = support.value
                // window.open(support.value)
            } else {
                push('/tel')
            }
        }
        getsupport().then(res => {
            if(res.code === 0) {
                support.value = res.data[0]?.url
            }
        })

        const getDd = async () => {
            if(loading.value) return false
            loading.value = true
            loadText.value = t('msg.zzszsj')
            loadImg.value = require('@/assets/images/1.gif')
            let submit = null
            let time = (info.value.deal_zhuji_time || 1)*1000
            let time2 = (info.value.deal_shop_time || 2)*1000
            setTimeout(async () => {
                loadText.value = t('msg.zzppsp')
                loadImg.value = require('@/assets/images/2.gif')
                submit = await submit_order()
                setout(submit, time2)
            }, time);
        }
        const setout = (json,time) => {
            setTimeout(()=> {
                if (json) {
                    if(json.code === 0) {
                        loadImg.value = require('@/assets/images/3.gif')
                        loadText.value = t('msg.ppcg')
                        setTimeout(()=>{
                            proxy.$Message({message: json.info, type: 'success'})
                            tjOrder(json)
                        }, 1000)
                    } else {
                        proxy.$Message({message: json.info, type: 'error'})
                        loading.value = false
                    }
                    console.log(json)
                } else {
                    setout(json,time)
                }
            },time)
        } 
        return {info,currency,level_show,loading,getDd,clickRight,confirmPwd,tjOrder,showTj,onceinfo,formatTime,cancelPwd,content, loadText,status_list,loadImg}
    }
}
</script>
<style lang="scss" scoped>
.obj{
    background-color: $theme;
    display: flex;
    flex-direction: column;
    padding-bottom: 0 !important;
    padding-top: calc(var(--van-nav-bar-height) + 20px);
    .content{
        flex: 1;
        // margin-top: 30px;
        background-color: #fff;
        padding: 30px;
        overflow: auto;
        border-top-left-radius: 30px;
        border-top-right-radius: 30px;
        padding-bottom:140px;
        color: #333;
        .money{
            display: flex;
            justify-content: space-between;
            .left{
                .top{
                    font-size: 60px;
                    .span{
                        margin-left: 3px;
                    }
                }
                .text{
                    font-size: 16px;
                    color: #999;
                    text-align: left;
                }
            }
            .right{
                position: relative;
                .van-button--primary{
                    width: 100px;
                    height: 80%;
                    border-radius: 40px;
                }
                .img{
                    width: 90px;
                    position: absolute;
                    right: 0;
                    top: 50%;
                    transform: translateY(-50%);
                }
            }
        }
        .vip{
            border-radius: 20px;
            box-shadow: $shadow;
            position: relative;
            color: #fff;
            margin-top: 40px;
            padding: 30px;
            background-color: $theme;
            .right{
                position: absolute;
                right: 30px;
                top: 30px;
                width: 40px;
            }
            .top{
                display: flex;
                margin-bottom: 50px;
                // .l{
                //     width: 65px;
                //     margin-right: 20px;
                // }
                .r{
                    display: flex;
                    flex-direction: column;
                    justify-content: space-around;
                    .title{
                        font-size: 60px;
                        text-align: left;
                        margin-bottom: 30px;
                    }
                    .sub{
                        font-size: 32px;
                        margin-bottom: 5px;
                        text-align: left;
                        &.s1{
                            font-size: 18px;
                        }
                    }
                }
            }
            .b{
                font-size: 56px;
                text-indent: 4px;
                .span{
                    background: limegreen;
                    padding: 5px 8px;
                }
            }
        }
        .yonj{
            display: flex;
            flex-wrap: wrap;
            box-shadow: $shadow;
            border-radius: 24px;
            padding:0 24px;
            .li{
                width: 50%;
                text-align: left;
                padding:24px 0;
                border-bottom: 1px solid #ccc;
                
                .top{
                    font-size: 30px;
                    color: #333;
                    // margin-bottom: 20px;
                }
                .text{
                    font-size: 14px;
                    color: #999;
                }
                &:nth-child(2n){
                    text-align: right;
                }
            }
            .btnn{
                width: 100%;
                padding: 10px 0 ;
                // border: 1px solid #999;
                .ksqg{
                    height: 85px;
                    line-height: 85px;
                    font-size: 30px;
                    border-radius: 0;
                    .img{
                        vertical-align: middle;
                    }
                }
            }

        }
        .qd{
            margin-top: 40px;
            text-align: left;
            .title{
                font-size: 28px;
                font-weight: 600;
                color: #333;
            }
            .sub{
                font-size: 24px;
                line-height: 30px;
                margin-top: 10px;
                color: #333;
            }
        }
    }
    .hy_box{
        height: 230px;
        width: 100%;
        padding: 25px;
        color: #fff;
        background-image: url('~@/assets/images/home/hybj.png');
        background-size: 100% 100%;
        border-radius: 10px;
        overflow: hidden;
        position: relative;
        text-align: left;
        .t{
            margin-bottom: 18px;
            .img{
                width: 65px;
                height: auto;
                margin-right: 20px;
                vertical-align: middle;
            }
            .text{
                font-size: 27px;
            }
        }
        .b{
            padding-left: 85px;
            font-size: 18px;
            .sub{
                .line{
                    margin: 0 22px;
                }
            }
        }
        .txlevel{
            position: absolute;
            right: 25px;
            top: 50%;
            transform: translateY(-50%);
            width: 150px;
            height: 60px;
            padding: 0;
            // line-height: 60px;
            font-size: 24px;
            color: #2620ce;
            background-color: #e2e6ff;
            border-radius: 20px;
            border: none;
        }
    }
    :deep(.van-dialog){
        .van-dialog__header{
            text-align: left;
            padding: 20px 40px;
            font-weight: 600;
        }
            .list{
                padding: 0 40px;
                box-shadow: none;
                max-height: 60vh;
                overflow: auto;
                display: flex;
                flex-direction: column;
                .tops {
                    margin-bottom: 0;
                    color: #333;
                    .span {
                        margin-right: 24px;
                    }
                }
                .box{
                    padding: 15px;
                    border: 2px solid #ccc;
                    margin-top: 24px;
                    &:first-child{
                        margin-top: 0;
                    }
                    .value0 {
                        padding: 3px 10px;
                        background-color: red;
                        color: #fff;
                    }
                    .value1 {
                        padding: 3px 10px;
                        background-color: #07c160;
                        color: #fff;
                    }
                }
            }
            .van-dialog__footer{
                margin-top: 40px;
                .van-dialog__confirm{
                    color: $theme;
                }
            }
    }
    .list{
        padding: 30px;
        box-shadow: $shadow;
        color: $subtext;
        text-align: left;
        margin-top: 40px;
        border-radius: 10px;
        .top{
            display: flex;
            justify-content: space-between;
            margin-bottom: 35px;
            .time{
                font-size: 16px;
            }
            .tab{
                font-size: 20px;
                color: $theme;
            }
        }
        .cet{
            display: flex;
            background-color: #fafafa;
            padding: 10px 0;
            .img{
                width: 100%;
                height: 180PX;
            }
            .text{
                font-size: 20px;
            }
        }
        .monney{
            margin-top: 30px;
            .tent{
                display: flex;
                justify-content: space-between;
                font-size: 24px;
                .span{
                    width: 120px;
                    // text-align: justify;
                    // text-justify: distribute-all-lines; 
                    // text-align-last: justify; 
                    color: #333;
                }
            }
        }
        .van-button{
            font-size: 32px;
            margin-top: 50px;
        }
        
    }
}
.img_loading{
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    background-color: rgb(0,0,0);
    color: #fff;
    display: flex;
    flex-direction: column;
    justify-content: center;
    text-align: center;
    font-size: 32px;
    font-weight: 600;
    .img{
        width: 80%;
        background-color: rgba(0,0,0,0.5);
        margin: 0 auto 30px;
        border-radius: 12px;
    }
    z-index: 888;
}
</style>