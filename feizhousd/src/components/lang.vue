<template>
    <div class="footer">
        <img :src="require('@/assets/images/lang'+(color == 'white' ? 1 : '')+'.png')" class="lang" height="27" width="27" alt="" @click="showLang()">
        <div v-if="show">
            <van-dialog v-model:show="show" :showConfirmButton="false" closeOnClickOverlay>
                <div class="lang_box">
                    <!-- <img :src="require('@/assets/images/register/lang_bg.png')" class="lang_bg" /> -->
                    <div class="title">{{$t('msg.check_lang')}}</div>
                    <div class="content">
                        <!-- <img :src="require('@/assets/images/register/qiu.png')" class="qiu" /> -->
                        <div class="langs">
                            <span class="li" :class="langcheck==item.link && 'check'" v-for="(item,index) in langs" :key="index"  @click="handSeletlanguages(item)">
                                <!-- <img :src="item.image_url" class="img" height="18" width="27" alt=""> -->
                                <span class="text">{{item.name}}</span>
                            </span>
                        </div>
                        <div class="btn">
                        <!-- <van-button round block type="primary" @click="submitLang">
                            {{$t('msg.nowQh')}}
                        </van-button> -->
                        </div>
                    </div>
                </div>
            </van-dialog>
        </div>
    </div>
</template>
<script>
import { ref,reactive,getCurrentInstance } from 'vue';
import store from '@/store/index'
import { useRouter } from 'vue-router';
import {vantLocales} from '@/i18n/i18n';
import { useI18n } from 'vue-i18n'
export default {
    props: {
        color: String
    },
    setup(){
        // 语言切换
        const {proxy} = getCurrentInstance()
        const { locale,t } = useI18n()
        const langcheck = ref(store.state.lang)
        const langImg = ref('')
        console.log(langcheck.value)
        langImg.value = store.state.langImg
        const langs = ref(store.state.baseInfo?.languageList)
        const show = ref(false);

        const showLang = () => {
            langcheck.value =store.state.lang
            show.value = true
            console.log(langcheck.value)
        }
        const handSeletlanguages = (row) => {
            // langcheck.value = 'bn_bd'
            langcheck.value = row.link
            langImg.value = row.image_url
            submitLang()
        }
        const submitLang = () => {
            locale.value = langcheck.value
            store.dispatch('changelang',langcheck.value)
            // locale.value = 'zh_cn'
            // store.dispatch('changelang','zh_cn')
            store.dispatch('changelangImg',langImg.value)
            vantLocales(locale.value)
            show.value = false
            proxy.$Message({ type: 'success', message: t('msg.switch_lang_success') });
        }
        return {show,submitLang,handSeletlanguages,langs, showLang, langcheck}
    }
}
</script>
<style lang="scss" scoped>
    .lang_box{
        width: 100%;
        position: relative;
        padding-top: 60px;
        .lang_title {
            margin-bottom: 40px;
        }
        .lang_bg{
        width: 100%;
        position: absolute;
        top: 0;
        left: 0;
        }
        .content{
        position: relative;
        z-index: 1;
        text-align: center;
        .qiu{
            width: 175px;
            border-radius: 50%;
            box-shadow: $shadow;
            margin-bottom: 6px;
        }
        .langs{
            margin-bottom: 15px;
            max-height: 70vh;
            overflow: auto;
            border: 1px solid #ccc;
            margin: 24px;
            border-radius: 24px;
            .li{
                padding: 24px;
                display: block;
                text-align: left;
                max-height: 500px;
                overflow: auto;
                border-bottom: 1px solid #ccc;
                &:last-child{
                    border-bottom: none;
                }
                &.ctn{
                    padding: 24px;
                }
                &.check{
                    background-color: #ccc;
                }
                .img{
                    margin-right: 34px;
                    vertical-align: middle;
                }
                .text{
                    font-size: 26px;
                    color:$textColor;
                }
            }
        }
        .btn{
            padding: 50px 54px 50px;
        }
        }
    }
</style>