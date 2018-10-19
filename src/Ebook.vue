<template>
    <div class="ebook">
        
        <title-bar :ifTitleAndMunuShow="ifTitleAndMunuShow"></title-bar>
        <div class="read-wrapper" >
            <div id="read"></div>
            <div class="mask">
                <div class="left" @click="prevPage"></div>
                <div class="center" @click="toggleTitleAndMenu"></div>                
                <div class="right" @click="nextPage"></div>
            </div>
        </div>
        <menu-bar @setFontSize="setFontSize" 
                    :defaultFontSize="defaultFontSize" 
                    :fontSizeList="fintSizeList" 
                    :ifTitleAndMunuShow="ifTitleAndMunuShow" 
                    :themeList="themeList"
                    :defaultTheme="defaultTheme"
                    @setTheme="setTheme"
                    :bookAvailable="bookAvailable"
                    @onProgressChange="onProgressChange"
                    :navigation="navigation"
                    @jumpTo="jumpTo"
                    ref="menuBar"></menu-bar>
        
    </div>
</template>

<script>
    import TitleBar from '@/components/TitleBar'
    import MenuBar from '@/components/MenuBar'
    import Epub from 'epubjs'
import { prerelease } from 'semver';
    const DOWNLOAD_URL='/static/first_book.epub'
    global.ePub=Epub;
    export default {
        data(){
            return{
                ifTitleAndMunuShow:false,
                fintSizeList:[
                    {fontsize:12},
                    {fontsize:14},
                    {fontsize:16},
                    {fontsize:18},
                    {fontsize:20},
                    {fontsize:22},
                    {fontsize:24}
                ],
                defaultFontSize:16,
                themeList:[
                    {
                        name:'default',
                        style:{
                            body:{
                                'color':'#000',
                                'background':'#fff'
                            }
                        }
                    },
                    {
                        name:'eye',
                        style:{
                            body:{
                                'color':'#000',
                                'background':'#ceeaba'
                            }
                        }
                    },
                    {
                        name:'night',
                        style:{
                            body:{
                                'color':'#fff',
                                'background':'#000'
                            }
                        }
                    },
                    {
                        name:'gold',
                        style:{
                            body:{
                                'color':'#000',
                                'background':'rgb(241,236,216)'
                            }
                        }
                    }
                ],
                defaultTheme:0,
                //图书是否可用
                bookAvailable:false,
                navigation:{}
            }
        },
        components:{
            TitleBar,
            MenuBar
        },
        methods:{
            //根据链接跳转到指定位置
            jumpTo(href){
                this.rendition.display(href)
                this.hideTitleAndMenu()
            },
            hideTitleAndMenu(){
                //隐藏标题栏和菜单栏
                this.ifTitleAndMunuShow=false;
                //隐藏菜单栏弹出的设置
                this.$refs.menuBar.hideSetting();
                //隐藏目录
                this.$refs.menuBar.hideContent()
            },
            //进度条数值progress（0-100）
            onProgressChange(progress){
                const percentage=progress/100
                const location=percentage>0 ? this.locations.cfiFromPercentage(percentage):0;
                this.rendition.display(location)
            },
            setTheme(index){
                this.themes.select(this.themeList[index].name)
                this.defaultTheme=index
            },
            registerTheme(){
                this.themeList.forEach(theme=>{
                    this.themes.register(theme.name,theme.style)
                })
            },
            setFontSize(fontSize){
                this.defaultFontSize=fontSize;
                if(this.themes){
                    this.themes.fontSize(fontSize)
                }
            },
            //电子书的解析和渲染
            showEpub(){
                //生成Book
                this.book=new Epub(DOWNLOAD_URL)
                //生成Rendition
                this.rendition=this.book.renderTo('read',{
                    width:window.innerWidth,
                    height:window.innerHeight
                })
                //生成Rendition.display渲染
                this.rendition.display()
                //获取theme对象
                this.themes=this.rendition.themes
                //设置默认字体
                this.setFontSize(this.defaultFontSize)
                //this.themes.register(name,style)
                //this.themes.select(name)
                this.registerTheme()
                this.setTheme(this.defaultTheme)
                //获取locations对象
                //通过epubjs的钩子函数来实现
                this.book.ready.then(()=>{
                    this.navigation=this.book.navigation
                    console.log(this.navigation)
                    return this.book.locations.generate()
                }).then(result=>{
                    this.locations=this.book.locations
                    this.bookAvailable=true
                })
            },
            prevPage(){
                if(this.rendition){
                    this.rendition.prev()
                }
            },
            nextPage(){
                if(this.rendition){
                    this.rendition.next()
                }
            },
            toggleTitleAndMenu(){
                this.ifTitleAndMunuShow=!this.ifTitleAndMunuShow
                if(!this.ifTitleAndMunuShow){
                    this.$refs.menuBar.hideSetting()
                }
            }
        },
        mounted(){
            this.showEpub()
        }
    }
</script>

<style lang='scss' scoped>
@import 'assets/style/global';
.ebook{
    position:relative;
    .read-wrapper{
        .mask{
            position: absolute;
            top:0;
            left:0;
            z-index: 100;
            display: flex;
            width: 100%;
            height:100%;
            .left{
                flex:0 0 px2rem(100);
            }
            .center{
                flex: 1;
            }
            .right{
                flex:0 0 px2rem(100);
            }
        }
    }
}
</style>