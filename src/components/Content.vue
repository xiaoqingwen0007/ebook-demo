<template>
    <transition name="slide-right">
        <div class="content">
            <div class="content-wrapper" v-if="bookAvailable">
                <div class="content-item" v-for="(item,index) in navigation.toc" :key="index" @click="jumpTo(item.href)">
                    <span class="text">{{item.label}}</span>
                </div>
            </div>
            <div class="empty" v-else>加载中...</div>
        </div>
    </transition>
</template>

<script>
    export default {
        props:{
            ifShowContent:Boolean,
            navigation:Object,
            bookAvailable:Boolean
        },
        methods:{
            jumpTo(target){
                this.$emit("jumpTo",target)
            }
        }
    }
</script>

<style lang="scss" scoped>
@import '../assets/style/global';
.content{
    background:#fff;
    position: absolute;
    left:0;
    top:0;
    width:70%;
    height:100%;
    z-index: 104;
    @include center;
    .content-wrapper{
        position:relative;
        height:100%;
        width:100%;
        overflow:auto;
        .content-item{
            width:100%;
            line-height:px2rem(30);
            .text{
                font-size:px2rem(14);
                padding-left:px2rem(10)
            }
        }
    }
    .empty{
        font-size:px2rem(16)
    }
}
</style>