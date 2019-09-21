<template>
  <div class="home">
    <my-header 
        @coverageCont="coverageCont"
    />
    <main>
        <my-left
            @changePenType="changePenType"
        />
        <my-canvas />
        <my-right 
            :nowColor="ctxColor"
            :ctxSize="ctxSize"
            :nowCoverage="nowCoverage"
            @changeColor="changeColor"
            @changeSize="changeSize"
        />
    </main>
    <div class="img-box"  style="display: none;">
        <input type="file" id="imageGo" accept="image/*" >
        <img src="http://curtaintan.club/headImg/1549358122065.jpg" alt="" id="imgEle" >
    </div>
    <Modal
        v-model="modal_show"
        title="抱歉~~~~"
        @on-ok="modalClick"
        @on-cancel="modalClick">
        <p>截图功能暂不开发了--</p>
        <p>你可以在下面的网址体验canvas原版</p>
        <p>https://curtaintan.github.io/drawing-board/</p>
        <a href="https://curtaintan.github.io/drawing-board/" target="_blank" >快到碗里来！！</a>
    </Modal>
  </div>
</template>

<script>

import myHeader from "../components/header"
import myLeft from "../components/left"
import myRight from "../components/right"
import myCanvas from "../components/canvas"

export default {
  name: 'home',
  data() {
      return {
          ctx: null,
          isDrawing: false,
          ctxColor: "#16a085",
          ctxSize: 6,
          penType: 'pen',
          history: [],
          step: -1,
          nowCoverage: null,
          textBox: null,
          drawingObject: null,
          drawPointer: {
              x : 0,
              y : 0
          },
          modal_show: false
      }
  },
  components: {
      myHeader,
      myLeft,
      myRight,
      myCanvas
  },
  methods: {
      changeColor( color ){
          this.ctxColor = color
          this.ctx.freeDrawingBrush.color = color
      },
      changeSize( size ){
          this.ctxSize = parseInt( size )
          this.ctx.freeDrawingBrush.width = parseInt( size )
      },
      modalClick(){

      },
      coverageCont(data){
          if( parseInt( data ) === 0 ){
              if( this.nowCoverage ){
                  this.nowCoverage.bringForward()
              }
          }
          if( parseInt( data ) === 1 ){
              if( this.nowCoverage ){
                  this.nowCoverage.sendBackwards()
              }
          }
          if( parseInt( data ) === 2 ){
              if( this.nowCoverage ){
                  this.nowCoverage.bringToFront()
              }
          }
          if( parseInt( data ) === 3 ){
              if( this.nowCoverage ){
                  this.nowCoverage.sendToBack()
              }
          }
          if( parseInt( data ) === 4 ){
              if( this.nowCoverage ){
                  if(  Array.isArray( this.nowCoverage )  ){
                      for( let i = 0; i < this.nowCoverage.length; i ++ ){
                          this.ctx.remove( this.nowCoverage[i] )
                          this.ctx.discardActiveObject()
                      }
                  } else {
                    this.ctx.remove( this.nowCoverage )
                  }
                  this.nowCoverage = null
                  this.addHistory()
              }
          }
          if( parseInt( data ) === 5 ){
              if( this.nowCoverage ){
                  this.saveImageFun( this.nowCoverage )
              }
          }
      },
      changePenType( type ){
          switch( type ){
                case "icon-shubiao":
                    this.shubiaoFun()
                    break
                case "icon-pen":
                    this.penFun()
                    break
                case "icon-line1":
                    this.lineFun()
                    break
                case "icon-arrow-right":
                    this.arrowFun()
                    break
                case "icon-juxing":
                    this.junxingFun()
                    break
                case "icon-clear2":
                    this.clear()
                    break
                case "icon-wenzi":
                    this.textFun()
                    break
                case "icon-caijian":
                    this.cutFun()
                    break
                case "icon-tupian1":
                    this.picFun()
                    break
                case "icon-down-load":
                    this.downloadFun()
                    break
                case "icon-undo1":
                    this.undo()
                    break
                case "icon-redo2":
                    this.redo()
                    break
          }
          if( this.textBox ){
                this.textBox.exitEditing()
                this.textBox = null
            }
      },
      shubiaoFun(){
          this.ctx.isDrawingMode = false
          this.penType = "shubiao"
          this.ctx.selection = true
          this.ctx.skipTargetFind = false
      },
      penFun(){
          this.ctx.isDrawingMode = true
          this.penType = "pen"
      },
      lineFun(){
          this.penType = "line"
          this.ctx.skipTargetFind = true
          this.ctx.selection = false
          this.ctx.isDrawingMode = false
      },
      arrowFun(){
          this.penType = "arrow"
          this.ctx.skipTargetFind = true
          this.ctx.selection = false
          this.ctx.isDrawingMode = false
      },
      junxingFun(){
          this.penType = "juxing"
          this.ctx.skipTargetFind = true
          this.ctx.selection = false
          this.ctx.isDrawingMode = false
      },
      clear(){
          this.ctx.clear()
          this.step = 0
          this.history.length = 1
      },
      textFun(){
          this.ctx.isDrawingMode = false
          this.ctx.skipTargetFind = true
          this.penType = "text"
      },
      picFun(){
          imageGo.click()
      },
      cutFun(){
          this.modal_show = true
      },
      downloadFun(){
            this.saveImageFun( this.ctx )
      },
      undo(){
          if( this.step > 0 ){
            this.renderHistory( --this.step )
          } else {
              this.$Notice.open({
                    title: '提示',
                    desc: '这是最开始的画布，不能再撤销了. '
                });
          }
      },
      redo(){
          if( this.step + 1 < this.history.length ){
              this.renderHistory( ++ this.step )
          } else {
              this.$Notice.open({
                    title: '提示',
                    desc: '这是最新的画布，不能再前进了. '
                });
          }
      },
      createText( e ){
            this.textBox = new fabric.Textbox( "", {
              left: e.pointer.x,
              width: 150,
              top: e.pointer.y,
              borderColor: "#2c2c2c",
              fill: this.ctxColor,
              fontSize: this.ctxSize * 2,
              hasControls: false
          } )
        //   textBox.editable = true
          this.ctx.add( this.textBox )
          this.textBox.enterEditing()
          this.textBox.hiddenTextarea.focus()
      },
      addHistory(){
            this.history.push( this.ctx.toDatalessObject() )
            this.step ++
      },
      renderHistory( step ){
          this.ctx.loadFromDatalessJSON( this.history[ step ] )
      },
      saveImageFun( content ){
          let saveA = document.createElement('a')
            document.body.appendChild(saveA)
            saveA.href = content.toDataURL()
            saveA.download = 'canvas-'+(new Date).getTime()
            saveA.target = '_blank'
            saveA.click()
      },
      mouseDownFun( e ){ {
            if( this.step + 1 !== this.history.length ){
                this.history.length = this.step + 1
            }
            if( this.penType === "pen" ){
                this.isDrawing = true
            }
            if( this.penType === "text" ){
                this.createText( e )
            }
            if( this.penType === "line" || this.penType === "arrow" || this.penType === "juxing" ){
                this.isDrawing = true
                this.drawPointer.x = e.pointer.x
                this.drawPointer.y = e.pointer.y
            }
        }
      },
      selectActionFun( e ){
            if( e.selected.length !== 1  ){
                this.nowCoverage = e.selected
            } else {
                this.nowCoverage = e.selected[0]
            }
      },
      mouseUpFun( e ) {
            if( this.drawingObject ){
                this.ctx.add( this.drawingObject )
                this.drawingObject = null
            }
            if( this.isDrawing ){
                this.addHistory()
            }
            this.isDrawing = false
            this.drawingObject = null
      },
      drawingFun( e ){
          if( !this.isDrawing ){
              return
          }
          if( this.drawingObject ){
              this.ctx.remove( this.drawingObject )
          }
          var canvasObject = null
          if( this.penType === "line" ){
              console.log( "现在的步数", this.step )
              canvasObject = new fabric.Line([this.drawPointer.x, this.drawPointer.y, e.pointer.x, e.pointer.y ], {
                stroke: this.ctxColor,
                strokeWidth: this.ctxSize
              })
          }
          if( this.penType === "arrow" ){
              console.log( "箭头模式" )
                canvasObject = new fabric.Path( this.drawArrow(this.drawPointer.x, this.drawPointer.y,  e.pointer.x, e.pointer.y, 30, 30), {
                    stroke: this.ctxColor,
                    fill: "rgba(255,255,255,0)",
                    strokeWidth: this.ctxSize
                });
          }
          if( this.penType === "juxing" ){
              var path =
                "M " +
                this.drawPointer.x +
                " " +
                this.drawPointer.y +
                " L " +
                e.pointer.x +
                " " +
                this.drawPointer.y +
                " L " +
                e.pointer.x +
                " " +
                e.pointer.y +
                " L " +
                this.drawPointer.x +
                " " +
                e.pointer.y +
                " L " +
                this.drawPointer.x +
                " " +
                this.drawPointer.y +
                " z";
                canvasObject = new fabric.Path(path, {
                    left: this.drawPointer.x,
                    top: this.drawPointer.y,
                    stroke: this.ctxColor,
                    strokeWidth: this.ctxSize,
                    fill: "rgba(255, 255, 255, 0)"
                })
          }
            if( canvasObject ){
                this.ctx.add( canvasObject )
                this.drawingObject = canvasObject
            }
      },
    //   selectionGroup( e ){
    //       console.log("分组选中")
    //       console.log( e )
    //     //   if( e.selected.length > 1 ){
    //             var group = new fabric.Group( [...e.selected], {
    //             top: e.target.top < 0 ? Math.abs( e.target.top ) * 2 : e.target.top ,
    //             left: e.target.left < 0 ? Math.abs( e.target.left ) * 2 : e.target.left 
    //                 // originX: 'left',
    //                 // originY: 'top'
    //             })
    //         this.nowCoverage = group
    //     //   } else {
    //     //       this.nowCoverage = e.selected[0]
    //     //   }

    //     //   group.cloneAsImage( ( e ) => {
    //     //       console.log( "克隆的对象为Image" )
    //     //       console.log( e )
    //     //       this.nowCoverage = e
    //     //   } )
    //     //   var x = new Object.create
    //     //   fabric.Group.fromObject( group, ( ooo ) => {
    //     //     console.log( "创建成功" )
    //     //     console.log( ooo )
    //     //     this.nowCoverage = ooo
    //     // } )
    //     //   console.log("分组选中")
    //   },
      drawArrow(fromX, fromY, toX, toY, theta, headlen) {
        theta = typeof theta != "undefined" ? theta : 30;
        headlen = typeof theta != "undefined" ? headlen : 10;
        // 计算各角度和对应的P2,P3坐标
        var angle = Math.atan2(fromY - toY, fromX - toX) * 180 / Math.PI,
        angle1 = (angle + theta) * Math.PI / 180,
        angle2 = (angle - theta) * Math.PI / 180,
        topX = headlen * Math.cos(angle1),
        topY = headlen * Math.sin(angle1),
        botX = headlen * Math.cos(angle2),
        botY = headlen * Math.sin(angle2);
        var arrowX = fromX - topX,
        arrowY = fromY - topY;
        var path = " M " + fromX + " " + fromY;
        path += " L " + toX + " " + toY;
        arrowX = toX + topX;
        arrowY = toY + topY;
        path += " M " + arrowX + " " + arrowY;
        path += " L " + toX + " " + toY;
        arrowX = toX + botX;
        arrowY = toY + botY;
        path += " L " + arrowX + " " + arrowY;
        return path;
    }
  },
  mounted(){
        
    var parentCan = can.parentNode
    can.width = parentCan.clientWidth
    can.height = parentCan.clientHeight

    this.ctx = new fabric.Canvas( "can" )
    this.ctx.isDrawingMode = true
    this.ctx.freeDrawingBrush.color = this.ctxColor
    this.ctx.freeDrawingBrush.width = this.ctxSize
    // this.ctx.renderOnAddRemove = true
    this.ctx.preserveObjectStacking = true    // 选中图层不置顶

    this.addHistory()

    this.ctx.on( "mouse:down", this.mouseDownFun )

    this.ctx.on( "object:modified", () => {
        this.addHistory()
    })

    this.ctx.on( "mouse:up", this.mouseUpFun )

    this.ctx.on( "mouse:move", this.drawingFun )

    this.ctx.on( "selection:created", this.selectActionFun )
    this.ctx.on( "selection:updated", this.selectActionFun )

    imageGo.addEventListener( "change", ( data ) => {
        var files = imageGo.files
        var myrender = new FileReader()
        if( files && files[0] ){
            myrender.onload = ( ev ) => {
                imgEle.src = ev.target.result
                setTimeout( () => {
                    fabric.Image.fromURL( imgEle.currentSrc, ( oImg ) => {
                        oImg.set({
                            left:100,
                            top:100,
                            centeredScaling:true,
                            cornerSize: 7,
                            cornerColor: "#9cb8ee",
                            transparentCorners: false,
                        })
                        oImg.scaleToWidth( 300 )
                        oImg.scaleToHeight( 200 )
                        this.ctx.add( oImg )
                        this.addHistory()
                    } )
                }, 0 )
            }
            myrender.readAsDataURL(files[0])
        }
    } )

  }
}
</script>


<style lang="scss">
    .home{
        height: 100vh;
        background-color: rgb(238, 238, 238);
        padding-bottom: 20px;
        main{
            display: flex;
            justify-content: space-between;
            height: calc( 100% - 80px );
        }
    }
</style>
