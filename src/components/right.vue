<template>
    <div class="home-right">
        <h2>辅助栏</h2>
        <div class="my-color-box">
            <Divider orientation="left">画笔参数</Divider>
            <div class="now-color">
                <span>当前颜色：</span>
                <div class="dot-color" :style=" 'background-color:' + nowColor " ></div>
            </div>
            <div class="title-text">
                <span>颜色选择：</span>
            </div>
            <div class="default-color-box">
                <div 
                    v-for="(item, index) in colors" 
                    :key="index" 
                    :class=" nowColorIndex === index ? 'color-item action' : 'color-item' " 
                    :style=" 'background-color:' + item " 
                    @click="changColor(index)"
                ></div>
            </div>
            <div class="ui-select">
                <span>颜色选择器：</span>
                <ColorPicker 
                    editable
                    v-model="color1" 
                    alpha
                    hue
                    @on-change="setColor"
                />
            </div>
            <div class="size-box">
                <span>画笔大小：</span>
                <Slider v-model="size" @on-change="changeSize" :max="sizeMax" :min="sizeMin" ></Slider>
            </div>
        </div>
        <div class="card-contentbox">
            <Divider orientation="left">图层</Divider>
            <div class="top-canvas-box" >
                <canvas id="tcan"></canvas>
            </div>
        </div>
    </div>
</template>

<script>

// import { clone } from "../utils/deepclone.js" 

export default {
    props: {
        nowColor: {
            type: String,
            default: '#000'
        },
        ctxSize: {
            type: Number,
            default: 5
        },
        nowCoverage: {
            type: Object|Array,
            default: null
        }
    },
    data() {
        return {
            color1: 'rgba(25, 190,107, .5)',
            size: 5,
            color2: "",
            sizeMax: 20,
            sizeMin: 0,
            ctx2: null,
            colors: [
                "#000",
                "#2c3e50",
                "#8e44ad",
                "#3498db",
                "#2ecc71",
                "#f39c12",
                "#16a085",
                "#e74c3c"
            ]
        }
    },
    watch: {
        nowCoverage( coverageData ){
            if( coverageData ){
            if( Array.isArray( coverageData ) ){
                console.log( "是数组" )
                // var url = coverageData[0].toDataURL()
                // fabric.Image.fromURL( url , ( Oimg ) => {
                //     Oimg.scale(0.5)
                //     this.ctx2.add( Oimg )
                // } )
                
                // group.scaleToWidth( 120 )
                // group.scaleToHeight( 90 )
                // this.ctx2.add( group )
            } else {
                this.ctx2.clear()
                var url = coverageData.toDataURL()
                fabric.Image.fromURL( url , ( Oimg ) => {
                    var k = Oimg.width < 100 ? 1 : 240 / Oimg.width
                    var y = Oimg.height < 50 ? 1 : 120 / Oimg.height
                    var sacle = k > y ? y : k 
                    Oimg.scale( sacle )
                    var offsetLeft = Oimg.width > 240 ? 20 : ( 240 - Oimg.width * sacle ) / 2
                    var offsetTop = Oimg.height > 120 ? 15 : ( 120 - Oimg.height * sacle ) / 2
                    Oimg.set({
                        left: offsetLeft,
                        top: offsetTop
                    })
                    this.ctx2.add( Oimg )
                } )
            }
            }
        }
    },
    computed: {
        nowColorIndex() {
            var that = this
            var nowIndex = -2
            this.colors.forEach( ( element, index ) => {
                if( element === that.nowColor ){
                    nowIndex =  index
                }
            });
            return nowIndex
        }
    },
    methods: {
        changColor( index ){
            this.$emit( "changeColor", this.colors[index] )
        },
        setColor( color ){
            this.$emit( "changeColor", color )
        },
        changeSize( size ){
            this.$emit( "changeSize", size )
        },
        tan( arr ){
            console.log( coverageData )
            // var yu = clone( coverageData[0] )
            // console.log( "clone了" )
            // console.log( clonede )
            var group = new fabric.Group( [...coverageData])
            fabric.Group.fromObject( group, ( ooo ) => {
                console.log( "创建成功" )
                console.log( ooo )
                return 
            } )
        }
    },
    mounted(){
        var parentCan = tcan.parentNode
        tcan.width = 280
        tcan.height = 150
        this.ctx2 = new fabric.Canvas( "tcan" )
        this.ctx2.selection = false
        this.ctx2.skipTargetFind = true
    }
}
</script>


<style lang="scss">
    .home-right{
        width: 300px;
        background-color: #fff;
        margin: 0px 20px;
        border-radius: 4px;
        padding-top: 10px;
        .title-text{
            padding: 0px 20px;
            text-align: left;
        }
        .my-color-box{
            font-weight: 600;
            text-align: left;
            .now-color{
                display: flex;
                align-items: center;
                padding-left: 20px;
                .dot-color{
                    width: 30px;
                    height: 30px;
                    border-radius: 50%;
                    background-color: #888;
                    margin-left: 10px;
                }
            }
            .size-box{
                padding: 10px 20px;
            }
            .default-color-box{
                display: flex;
                flex-wrap: wrap;
                padding: 0px 10px;
                .color-item{
                    width: 40px;
                    height: 40px;
                    margin: 10px 15px;
                    border-radius: 50%;
                    position: relative;
                    cursor: pointer;
                    &.action::before{
                        content: "";
                        position: absolute;
                        width: 28px;
                        height: 28px;
                        top: 6px;
                        left: 6px;
                        border-radius: 50%;
                        border: 2px solid #fff;
                    }
                }
            }
            .ui-select{
                display: flex;
                align-items: center;
                padding: 5px 20px;
            }
        }
        .top-canvas-box{
            margin: 10px;
            background: url("../assets/canvas_bg.jpg");
            canvas{
                border: 1px dashed rgb(160, 160, 160);
                border-radius: 4px;
            }
        }
    }
</style>



