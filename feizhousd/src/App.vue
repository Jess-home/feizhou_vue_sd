<template>
  <my-scroll>
    <router-view/>
  </my-scroll>
</template>
<script>
import {common_parameters} from '@/api/login/index'
import { useI18n } from 'vue-i18n'
import store from '@/store/index'
import {vantLocales} from '@/i18n/i18n';
import myScroll from './components/scroll.vue'

export default {
  components:{myScroll},
  setup(){
    const { locale } = useI18n()
    // const {proxy} = getCurrentInstance()
     const changeFavicon = link => {
        let $favicon = document.querySelector('link[rel="icon"]');
        // If a <link rel="icon"> element already exists,
        // change its href to the given link.
        if ($favicon !== null) {
          $favicon.href = link;
          // Otherwise, create a new element and append it to <head>.
        } else {
          $favicon = document.createElement("link");
          $favicon.rel = "icon";
          $favicon.href = link;
          document.head.appendChild($favicon);
        }
      };
      Promise.resolve(common_parameters()).then(res=>{
        if(res.code === 0) {
          const info = JSON.parse(JSON.stringify(res.data))
            locale.value = info.language
            let languageList = info.languageList
            let json = languageList.find(rr => rr.link == info.language)
            let langImg = json && json.image_url
            store.dispatch('changelang',info.language)
            store.dispatch('changelangImg',langImg)
            store.dispatch('changebaseInfo',info)
            changeFavicon(info.site_icon); // 动态修改网站图标
            document.title = info.app_name; // 动态修改网站标题
            vantLocales(info.language)
        }
        }).catch(err=>{
          console.log(err)
      })

    return {}
  }
}
</script>
<style lang='scss'>
#app {
  font-family:"PingFang SC,Helvetica Neue,Helvetica,Arial,Hiragino Sans GB,Heiti SC,Microsoft YaHei,WenQuanYi Micro Hei,sans-serif"!important;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: $textColor;
  //background-image: linear-gradient(#d4dff5,#f3f7fd);
  height: 100vh;
}

</style>
