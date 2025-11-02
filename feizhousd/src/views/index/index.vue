<template>
    <div class="home_box">
        <router-view class="pore" @hideFooter="hideFooter"/>
        <footer-demo></footer-demo>
    </div>
    
</template>
<script>
import {ref} from 'vue'
import footerDemo from '@/components/footer.vue'
export default {
    components:{footerDemo},
    setup(){
        const showFooter = ref(true)
        console.log(showFooter.value)
        const hideFooter = (val) => {
            showFooter.value = !val
        }
        return{hideFooter,showFooter}
    }
}
</script>
<style lang="scss" scoped>
.home_box{
    height: 100vh;
    display: flex;
    flex-direction: column;
    .pore{
        flex: 1;
        overflow: auto;
        padding-bottom:140px;
        display: flex;
        flex-direction: column;
    }

}
</style>