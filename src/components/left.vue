<template>
    <div class="home-left">
        <ButtonGroup vertical>
            <Button 
            v-for="(item, index) in icons" 
            :key="index" 
            :title="title[index]" 
            size="large"
            :type=" nowType === index ? 'primary' : 'default' "
            @click="changeType(index)"
            >
                <Icon :class="'iconfont ' + item" size="22" />
            </Button>
        </ButtonGroup>
    </div>
</template>

<script>
export default {
    data(){
        return {
            icons: [
                "icon-shubiao",
                "icon-pen",
                "icon-line1",
                "icon-arrow-right",
                "icon-juxing",
                "icon-wenzi",
                "icon-tupian1",
                "icon-caijian",
                "icon-down-load",
                "icon-clear2",
                "icon-undo1",
                "icon-redo2"
            ],
            title: [
                "鼠标模式",
                "画笔模式",
                "直线",
                "箭头",
                "矩形",
                "文字",
                "插入图片",
                "截图",
                "下载",
                "清空画布",
                "撤销",
                "前进"
            ],
            nowType: 1
        }
    },
    methods: {
        changeType( e ){
            if( e < 6 ){
                this.nowType = e
            }
            this.$emit( "changePenType", this.icons[e] )
        }
    }
}
</script>




<style lang="scss">
    .home-left{
        width: 100px;
    }
</style>



