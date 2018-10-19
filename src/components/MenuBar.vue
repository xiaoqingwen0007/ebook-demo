<template>
    <div class="menu-bar">
        <transition name="slide-up">
            <div class="menu-wrapper" v-show="ifTitleAndMunuShow" :class="{'hide-box-shadow':ifIsSettingShow || !ifTitleAndMunuShow}">  
                <div class="icon-wrapper">
                    <span class="iconfont icon-caidan icon" @click="showSetting(3)"></span>
                </div>
                <div class="icon-wrapper">
                    <span class="iconfont icon-jindu icon" @click="showSetting(2)"></span>
                </div>
                <div class="icon-wrapper">
                    <span class="iconfont icon-taiyang1 icon" @click="showSetting(1)"></span>
                </div>
                <div class="icon-wrapper">
                    <span class="icon-a" @click="showSetting(0)">A</span>
                </div>
                
            </div>
            
        </transition>
        <transition name="slide-up">
            <div class="setting-wrapper" v-show="ifIsSettingShow">
                <div class="setting-font-size" v-if="showTag === 0">
                    <div class="preview" :style="{fontSize:fontSizeList[0].fontsize+'px'}">A</div>
                    <div class="select">
                        <div class="select-wrapper" v-for="(item, index) in fontSizeList" :key="index" @click="setFontSize(item.fontsize)">
                            <div class="line"></div>
                            <div class="point-wrapper">
                                <div class="point" v-show="defaultFontSize==item.fontsize">
                                    <div class="small-point"></div>
                                </div>
                            </div>
                            <div class="line"></div>
                        </div>
                    </div>
                    <div class="preview" :style="{fontSize:fontSizeList[fontSizeList.length-1].fontsize+'px'}">A</div>
                </div>
                <div class="setting-theme" v-else-if="showTag === 1">
                    <div class="setting-theme-item" v-for="(item,index) in themeList" :key="index" @click="setTheme(index)">
                        <div class="preview" :style="{background:item.style.body.background}" :class="{'no-border':item.style.body.background !== '#fff'}"></div>
                        <div class="text" :class="{'selected':index===defaultTheme}">{{item.name}}</div>
                    </div>
                </div>
                <div class="setting-progress" v-else-if="showTag===2">
                    <div class="progress-wrapper">
                        <input type="range"
                                max="100"
                                min="0"
                                step="1"
                                @change="onProgressChange($event.target.value)"
                                @input="onProgressInput($event.target.value)"
                                :value="progress"
                                :disabled="!bookAvailable"
                                ref="progress"
                                 class="progress"/>
                    </div>
                    <div class="text-wrapper">
                        <span>{{bookAvailable ? progress + "%" : '加载中...'}}</span>
                    </div>
                </div>
            </div>
        </transition>
        <content-view :ifShowContent="ifShowContent"
                        v-show="ifShowContent"
                        :navigation="navigation"
                        :bookAvailable="bookAvailable"
                        @jumpTo="jumpTo"></content-view>
        <transition name="fade">
            <div class="content-mask"
                        v-show="ifShowContent"
                        @click="hideContent"></div>
        </transition>
    </div>
</template>

<script>
    import ContentView from '@/components/Content'
    export default {
        components:{
            ContentView
        },
        props:{
            ifTitleAndMunuShow:Boolean,
            fontSizeList:Array,
            defaultFontSize:Number,
            themeList:Array,
            defaultTheme:Number,
            bookAvailable:Boolean,
            navigation:Object
        },
        data(){
            return {
                ifIsSettingShow:false,
                showTag:0,
                progress:0,
                ifShowContent:false
            }
        },
        methods:{
            hideContent(){
                this.ifShowContent=false
            },
            jumpTo(target){
                this.$emit("jumpTo",target)
            },
            //进度条松开后触发的事件，根据进度条数值跳转到指定位置
            onProgressChange(progress){
                console.log(2222)
                this.$emit('onProgressChange',progress)
            },
            //拖动进度条时触发的事件
            onProgressInput(progress){
                this.progress=progress
                this.$refs.progress.style.backgroundSize=`{this.progress}% 100%`
            },
            
            setTheme(index){
                this.$emit('setTheme',index)
            },
            setFontSize(fontSize){
                //this.$emit()调用父组件方法setFontSize值为fontSize
                this.$emit("setFontSize",fontSize)
            },
            showSetting(tag){
                if(tag===3){
                    this.ifIsSettingShow=false
                    this.ifShowContent=true
                }else{
                    this.ifIsSettingShow=true
                    this.showTag=tag
                }
                
            },
            hideSetting(){
                this.ifIsSettingShow=false
            }
        }
    }
</script>

<style lang="scss" scoped>
@import '../assets/style/global';
.menu-bar{
    .menu-wrapper{
        position:absolute;
        bottom:0;
        left:0;
        z-index: 102;
        height:px2rem(48);
        width:100%;
        background:#fff;
        display: flex;
        box-shadow: 0 px2rem(-8) px2rem(8) rgba(0,0,0,0.15);
        &.hide-box-shadow{
            box-shadow: none;
        }
        .icon-wrapper{
            flex:1;
            @include center;
            .icon-taiyang1{
                font-size:px2rem(24);
            };
            .icon-jindu{
                font-size:px2rem(28);
            };
            .icon-a{
                font-size:px2rem(20);
            }
        }
    }
    .setting-wrapper{
        position: absolute;
        bottom:px2rem(48);
        left:0;
        width:100%;
        height:px2rem(60);
        background: #fff;
        box-shadow: 0 px2rem(-8) px2rem(8) rgba(0,0,0,0.15);
        z-index: 101;
        .setting-font-size{
            display: flex;
            height:100%;
            
            .preview{
                flex:0 0 px2rem(30);
                @include center;
                &:first-child{
                    margin-left:px2rem(15)
                }
                &:last-child{
                    margin-right:px2rem(15)
                }
            }
            .select{
                display: flex;
                flex:1;
                .select-wrapper{
                    flex: 1;
                    display: flex;
                    align-items: center;
                    &:first-child{
                        .line{
                            &:first-child{
                                border-top:none;
                            }
                        }
                    }
                    &:last-child{
                        .line{
                            &:last-child{
                                border-top:none;
                            }
                        }
                    }
                    .line{
                        flex:1;
                        height:0;
                        border-top:px2rem(1) solid #ccc;
                    }
                    .point-wrapper{
                        flex:0 0 0 ;
                        width:0;
                        height:px2rem(7);
                        border-left:px2rem(1) solid #ccc;
                        position:relative;
                        .point{
                            position:absolute;
                            top:px2rem(-6);
                            left:px2rem(-8);
                            width: px2rem(20);
                            height:px2rem(20);
                            border-radius:50%;
                            background: #fff;
                            border-radius:px2rem(1) solid #ccc;
                            box-shadow: 0 px2rem(4) px2rem(4) rgba(0,0,0,0.15);
                            @include center;
                            .small-point{
                                height:px2rem(5);
                                width:px2rem(5);
                                background:#000;
                                border-radius:50%;
                               
                            }
                        }
                    }
                }
            }
            
        }
        .setting-theme{
            height:100%;
            display: flex;
            .setting-theme-item{
                flex:1;
                display: flex;
                flex-direction: column;
                padding:px2rem(5);
                box-sizing: border-box;
                .preview{
                    flex:1;
                    border:px2rem(1) solid #ccc;
                    &.no-border{
                        border:0;
                    }
                }
                .text{
                    flex:0 0 px2rem(30);
                    font-size:px2rem(12);
                    color:#ccc;
                    @include center;
                    &.selected{
                        color:#333;
                    }
                }
            }
        }
        .setting-progress{
            position:relative;
            width:100%;
            height:100%;
            .progress-wrapper{
                width:100%;
                height:100%;
                @include center;
                padding:0 px2rem(30);
                box-sizing:border-box;
                .progress{
                    width:100%;
                    -webkit-appearance: none;
                    height:px2rem(2);
                    background:-webkit-linear-gradient(#999,#999) no-repeat ,#ddd;
                    background-size:0 100%;
                    &:focus{
                        outline:none;
                    }
                    &::-webkit-slider-thumb{
                        -webkit-appearance: none;
                        height:px2rem(20);
                        width:px2rem(20);
                        border-radius:50%;
                        background:#fff;
                        box-shadow: 0 px2rem(4) px2rem(4) 0 rgba(0,0,0,0.15);
                        border:px2rem(1) solid #ddd;
                    }
                }
            }
            .text-wrapper{
                position: absolute;
                width:100%;
                top:px2rem(40);
                font-size:px2rem(14);
                @include center;
                color:#555;
            }
        }
    }
    .content-mask{
        position: absolute;
        top:0;
        left:0;
        z-index: 103;
        display: flex;
        width:100%;
        height:100%;
        background:rgba(51,51,51,0.8)
    }
}

</style>