<template>
    <div class="home-header">
        <div class="control-box">
            <ButtonGroup>
                <Button 
                    v-for="(item, index) in lists" 
                    :key="index" 
                    type="primary"
                    @click="myclick(index)"
                    >
                    {{item}}
                </Button>
            </ButtonGroup>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            lists: [
                "图层上移",
                "图层下移",
                "图层置顶",
                "图层置底",
                "删除图层",
                "下载图层",
            ]
        }
    },
    methods: {
        myclick( e ){
            console.log( e )
            this.$emit( "coverageCont", e )
        }
    }
}
</script>

<style lang="scss">
    .home-header{
        height: 80px;
        padding: 10px 100px;
        .control-box{
            height: 100%;
            display: flex;
            align-items: flex-end;
        }
    }
</style>

