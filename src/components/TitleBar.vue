<template>
    <transition name="slide-down">
        <div class="title-wrapper" v-show="ifTitleAndMunuShow">
            <div class="left">
                <div class="iconfont icon-xiangyou icon"></div>
            </div>
            <div class="right">
                <div class="icon-wrapper">
                    <span class="iconfont icon-gouwuche icon"></span>
                </div>
                <div class="icon-wrapper">
                    <span class="iconfont icon-geren icon"></span>
                </div>
                <div class="icon-wrapper">
                    <span class="iconfont icon-caidan1 icon"></span>
                </div>
            </div>
        </div>
    </transition>
</template>

<script>
    export default {
        props:{
            ifTitleAndMunuShow:Boolean,
        }
    }
</script>

<style lang='scss' scoped>
@import '../assets/style/global';
.title-wrapper{
    position:absolute;
    top:0;
    left:0;
    z-index: 101;
    height:px2rem(48);
    width:100%;
    background:#fff;
    display: flex;
    box-shadow: 0 px2rem(8) px2rem(8) rgba(0,0,0,0.15);
    .left{
        flex: 0 0 px2rem(60);
        @include center;
    };
    .right{
        flex: 1;
        display: flex;
        justify-content:flex-end;
        .icon-wrapper{
            flex:0 0 px2rem(40);
            @include center;
        }
    };
};
</style>