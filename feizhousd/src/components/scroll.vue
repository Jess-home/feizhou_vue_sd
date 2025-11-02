<template>
    <div id="wrapper" ref="wrapper">
        <slot></slot>
    </div>
</template>

<script>
    import BScroll from 'better-scroll'
    export default {
        name: "betterScroll",
        props: {
            handleToScroll: {
                type:Function,
                default:function () {}
            },
            handleToTouch:{
                type: Function,
                default:function () {}
            }
        },
        mounted() {
            this.$nextTick(()=>{
                var bscroll = new BScroll(this.$refs.wrapper,{
                    tap:true,
                    probeType:1
                });
                this.bscroll = bscroll;
                bscroll.on("scroll",(pos)=>{
                    this.handleToScroll(pos);
                });
                bscroll.on("touchEnd",(pos)=>{
                    this.handleToTouch(pos);
                })
            })

        },
        methods:{
            scrollToTop(y){
                this.bscroll.scrollTo(0,y);
            }
        }

    }
</script>

<style scoped>
    #wrapper{
        height: 100%;}
</style>

