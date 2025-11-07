<template>
  <div class="home">
    <van-nav-bar title="Web3" left-arrow @click-left="$router.go(-1)" @click-right="clickRight">
        <template #right>
            {{$t('msg.record')}}
        </template>
    </van-nav-bar>
    <div class="content">
        <div class="all_sy">
            <div class="top">
                <div class="money">
                    <span class="text">{{$t('msg.zongzichan')}}</span>
                    <span class="value">{{currency}} {{info?.ubalance}}</span>
                </div>
                <div class="money">
                    <span class="text">Web3{{ $t('msg.zichan') }}</span>
                    <span class="value">{{currency}} {{info?.balance}}</span>
                </div>
                <div class="money">
                    <span class="text">{{$t('msg.zsy')}}</span>
                    <span class="value">{{currency}} {{info.balance_shouru}}</span>
                </div>
                <div class="money">
                    <span class="text">{{$t('msg.zrsy')}}</span>
                    <span class="value">{{currency}} {{info.yes_shouyi}}</span>
                </div>
            </div>
            <div class="bottom">
                <div class="yezr">{{$t('msg.yezr')}}</div>
                <van-field
                    v-model="zr_money"
                    clearable
                    :placeholder="$t('msg.input_zr_money')"
                >
                    <template #left-icon>
                        {{currency}} 
                    </template>
                </van-field>
                <div class="yjsy">
                    <div class="l">
                        {{$t('msg.yjsy')}}
                    </div>
                    <div class="r">{{currency}} {{zr_money*lx_bili}}</div>
                </div>
            </div>

        </div>
        <div class="yezr">{{$t('msg.lccp')}}</div>
        <div class="list">
            <div class="li" v-for="item in info?.lixibao || []" :key="item.id" :class="lcid == item.id && 'check'" @click="lcid = item.id;lx_bili=item.bili*1">
                <div class="tent">
                    <div class="l">
                        {{item.day + $t('msg.day')}}
                    </div>
                    <div class="r">
                        <p>{{$t('msg.get_m')}} + {{item.bili*100 + '%'}}</p>
                        <p class="ll">{{$t('msg.sxf')}} {{item.shouxu*100}}%</p>
                        <p class="ll">{{$t('msg.min')}} {{currency}} {{item.min_num}}</p>
                        <p class="ll">{{$t('msg.max')}} {{currency}} {{item.max_num || 0}}</p>
                        <!-- <p class="ll">Free {{$t('msg.djxz')}}</p> -->
                    </div>
                </div>
            </div>
        </div>
        <div class="buttons">
            <van-button round block type="primary"  @click="onSubmit">
                <span class="span">{{$t('msg.zr')}}</span>
            </van-button>
        </div>
    </div>
  </div>
</template>

<script>
import { ref,getCurrentInstance } from 'vue';
import store from '@/store/index'
import {get_lixibao,lixibao_ru} from '@/api/home/index.js'
import { useRouter } from 'vue-router';
import { useI18n } from 'vue-i18n'
export default {
  name: 'address',
  setup() {
    const { t } = useI18n()
    const { push } = useRouter();
    const {proxy} = getCurrentInstance()
    const zr_money = ref('')
    const yjsy_money = ref(0)
    const lcid = ref(1)
    const lx_bili = ref(0.03)
    const info = ref({})
    const currency = ref(store.state.baseInfo?.currency)

    get_lixibao().then(res => {
        console.log(res)
        info.value = {...(res.data || {})}
    })

    const clickLeft = () => {
        push('/self')
    }
    const clickRight = () => {
        push('/libao_jl')
    }
    const onSubmit = async () => {
        let json = {
            price: zr_money.value,
            lcid: lcid.value
        }
        
        proxy.$dialog.confirm({
            title: t('msg.wxts'),
            message: t('msg.sure_zr'),
            confirmButtonText:t('msg.queren'),
            cancelButtonText:t('msg.quxiao')
        })
        .then(() => {
            lixibao_ru(json).then(res => {
                if(res.code === 0) {
                    proxy.$Message({ type: 'success', message:res.info});
                    zr_money.value = 0
                } else {
                    proxy.$Message({ type: 'error', message:res.info});
                }
            }).catch(rr => {
                console.log(rr)
            })
        })
        .catch(() => {
            // on cancel
        });
    };



    return {
        onSubmit,
        clickLeft,
        clickRight,
        zr_money,
        info,
        yjsy_money,
        lcid,
        lx_bili,
        currency
    };
  }
}
</script>

<style scoped lang="scss">
@import '@/styles/theme.scss';
.home{
    overflow: auto;
    background: #f5f6ff;
    margin-top: 100px;
    :deep(.van-nav-bar){
        background-color:$theme;
        color: #fff;
        position: absolute;
        left: 0;
        top: 0;
        width: 100%;
        .van-nav-bar__left{
            .van-icon{
                color: #fff;
            }
        }
        .van-nav-bar__title{
            color: #fff;
        }
        .van-nav-bar__right{
            img{
                height: 42px;
            }
        }
    }
    .content{
        padding: 0 30px;
        position: relative;
        .yezr{
            text-align: left;
            color: #333;
        }
        .all_sy{
            width: 100%;
            border-radius: 30px;
            padding-bottom: 54px;
            .top{
                padding: 54px 0;
                display: flex;
                justify-content: space-between;
                .money{
                    text-align: center;
                    display: flex;
                    flex-direction: column;
                    .text{
                        font-size: 28px;
                        color: #333;
                        margin-bottom: 20px;
                    }
                    .value{
                        font-size: 24px;
                        color: red;
                    }
                }
            }
            :deep(.bottom){
                .van-field{
                    margin-top: 20px;
                    padding: 20px 0;
                    border-bottom: 1px solid #ccc;
                    background-color: initial;
                    .van-field__left-icon{
                        margin-right: 30px;
                        display: flex;
                        flex-direction: column;
                        justify-content: center;
                    }
                    .van-field__control{
                        font-size: 24px;
                    }
                }
                .yjsy{
                    display: flex;
                    margin-top: 40px;
                    .l{
                        font-size: 22px;
                        color: #333;
                        margin-right: 20px;
                    }
                    .r{
                        font-size: 24px;
                        color: red;
                    }
                }
            }
        }
        .list{
            .li{
                width: 100%;
                display: flex;
                flex-direction: column;
                justify-content: center;
                background-color: #fff;
                margin-top: 20px;
                border-radius: 10px;
                color: #333;
                border: 1px solid #ccc;
                padding: 15px 0;
                &.check{
                    // box-shadow: 0 0 20px 0 $theme;
                    color: #fff;
                    background-color: #6d00be;
                }
                .tent{
                    display: flex;
                    justify-content: space-between;
                    padding: 0 58px;
                    .l{
                        font-size: 38px;
                        margin-right: 58px;
                        display: flex;
                        flex-direction: column;
                        justify-content: center;
                        .img{
                            height: 60px;
                            vertical-align: middle;
                        }
                    }
                    .r{
                        font-size: 24px;
                        flex: 1;
                        text-align: left;
                        .ll{
                            &:nth-child(2){
                                margin-top: 15px;
                            }
                        }
                    }
                }
            }
        }
        .buttons{
            padding: 60px 30px;
            .van-button{
                margin: 40px 0;
            }
        }
    }
}
</style>
