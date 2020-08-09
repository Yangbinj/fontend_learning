<template>
    <div class="CredPhotoTemplate">
        <p class="title">{{info.photoDesc}}</p>
        <ul class="addPhoto">
            <li v-for="(item , index) in imgList" @click="imagePreview(index)">
                <img :src="'data:image/png;base64,'+item"/>
                <img class="close" src="../assets/images/mcrm/close.png" @click.stop="close(index)"/>
            </li>
            <li v-if="showAdd">
                <img @click.stop="add" src="../assets/images/mcrm/NEW_ICON_55.svg"/>
            </li>
        </ul>
        <div style="clear: both"></div>
    </div>
</template>

<script>
    export default {
        name: 'CredPhotoTemplate',
        props: ['isBorder', 'info', 'index'],
        data () {
            return {
                showAdd: true,
                imgList: [],
                satisfy: false
            }
        },
        methods: {//方法
            imagePreview(index){
                var array = [];
                for (var i = 0; i < this.imgList.length; i++) {
                    array[i] = 'data:image/png;base64,' + this.imgList[i];
                }
                ImagePreview(
                {
                    images: array,
                    startPosition: index,
                    onClose() {
                        // do something
                    }
                });
            },
            close(index){
                this.imgList.splice(index, 1);
                this.check();
            },
            add(){
                window.AITakePhotoCallback = this.AITakePhotoCallback;
                if (this.isBorder == "1") {
                    this.sendPostMessage("AITakePhoto", {isBorder: 1});
                } else {
                    this.sendPostMessage("AITakePhoto", {isBorder: 0});
                }
            },
            AITakePhotoCallback(vertyPhotoByte){
                if (this.isIos()){
                    this.imgList.push(vertyPhotoByte);
                }else{
                    this.imgList.push(vertyPhotoByte.base64);
                }
                this.$parent.list[this.index] = this.imgList;
                this.check();
            },
            check(){
                if (this.imgList.length >= this.info.photoCountMin) {
                    if (!this.satisfy) {
                        this.$parent.satisfyNum += 1;
                        this.satisfy = true;
                    }
                    if (this.imgList.length >= this.info.photoCountMax) {
                        this.showAdd = false;
                    } else {
                        this.showAdd = true;
                    }
                } else {
                    if (this.satisfy) {
                        if (this.imgList.length < this.info.photoCountMin) {
                            this.$parent.satisfyNum -= 1;
                            this.showAdd = true;
                            this.satisfy = false;
                        }
                    }
                }
                if (this.$parent.satisfyNum == this.$parent.needNum) {
                    this.$parent.nextDis = false;
                } else {
                    this.$parent.nextDis = true;
                }
            }
        },
        created(){//渲染前执行
        },
        mounted(){//渲染后执行
        },
        computed: {},
    }
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    @import "../assets/css/infoCollection.less";
</style>

