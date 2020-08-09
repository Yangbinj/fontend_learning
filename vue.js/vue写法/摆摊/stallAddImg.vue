<template>
  <div class="stallAddImgPage">
    <!--弹窗部分-->
    <mt-actionsheet
      :actions="actions"
      v-model="sheetVisible">
    </mt-actionsheet>
    <!--弹窗部分end-->

    <!--图片预览展示-->
    <div class="imgPreview" v-show="showImgPreview">
      <mt-swipe :auto="0" :defaultIndex="imgIndex" :continuous="false">
        <mt-swipe-item v-for="(item,index) in imageList" :key="index" @click.native.prevent.stop="cancelPreview">
          <img :src=" 'data:image/jpeg;base64,'+ item" alt="" width="100%">
        </mt-swipe-item>
      </mt-swipe>
    </div>

    <!--图片预览展示end-->

    <mt-header title="发布摆摊消息">
      <mt-button slot="left" @click.prevent.stop="backBussiness"><span class="icon-goBack"></span></mt-button>
      <mt-button slot="right" @click.prevent.stop="sendBtn"><span class="icon-headerSure"></span></mt-button>
    </mt-header>

    <section class="stallAddImg ios_mescroll">
      <div class="stallAddImgContent">
        <textarea placeholder="请输入发布内容" v-model="inputText"> </textarea>

        <div class="imgEdit">
          <div class="imgPage" v-for="(item,index) in imageList">
            <div class="imgClose" @click.prevent.stop="imgClose(index)"></div>
            <div @click.prevent.stop="showPreview">
              <img :src="'data:image/jpeg;base64,'+item" alt="" @click.prevent.stop="showPreview(index)">
            </div>

          </div>
          <div class="addImgBtn" v-show="showAddImages" @click.prevent.stop="addImages"></div>
        </div>
        <div class="listTable">
          <div class="tableLabel">是否为签到</div>
          <div class="tableContent">
            <div :class="[isSign ? 'checkDisabled':'', 'stallSwitch']">
              <mt-switch v-model="switchSign" @change="changeSwitch" :disabled='isSign'></mt-switch>
            </div>

          </div>
        </div>
        <div class="listTable">
          <div class="tableLabel">所在位置</div>
          <div class="tableContent">
            <div class="addPartner" @click.prevent.stop="changePosition"></div>

            <div class="stallText">{{positionName}}</div>

          </div>
        </div>
        <div class="listTable">
          <div class="tableLabel">提醒谁看</div>
          <div class="tableContent">
            <div class="addPartner" @click.prevent.stop="toRemindPeople"></div>
            <div class="stallText">{{remindPeople}}</div>

          </div>
        </div>
        <div class="listTable">
          <div class="tableLabel">是否关联摆摊</div>
          <div class="tableContent">
            <div :class="[isStall ? 'checkDisabled':'', 'stallSwitch']">
              <mt-switch v-model="switchStall" @change="changeSwitch" :disabled='isStall'></mt-switch>
            </div>

          </div>
        </div>

      </div>

    </section>


  </div>
</template>

<script>

  import {Indicator} from 'mint-ui'
  import {MessageBox} from 'mint-ui'

  //  import {test} from '../../../assets/js/test'
  //  import {getPicture} from '../../../assets/js/cameraGetPicture'
  import {MapLoader, getLocation} from '../../../assets/js/Amap'

  import {mapGetters, mapMutations} from 'vuex'

  export default {
//    name: 'RetailOrderDetails',
    data () {
      return {
        inputText: '',
        switchSign: true,
        isSign: true,
        positionName: '',
        isStall: true,
        remindPeople: '提醒人员',
        switchStall: true,
        sheetVisible: false,
        actions: [],
        imageList: [],
        imgIndex: 0,
        showAddImages: true,
        showImgPreview: false,
        remindPeopleArr: null


        /* baseSrc: '/static/shopEdit.svg',
         baseSrcNew: ''*/
      }

    },
    computed: {
//        获取OPId
      ...mapGetters([
        'MCRM_opId',
        'MCRM_orgId',
        'MCRM_loginName',
        'MCRM_stallId',
        'MCRM_GDPosition',
      ])
    },
    created(){
      window.setImag = this.setImag
//      Indicator.open("加载中...")

      if (this.$route.params.stallPosition) {
        this.positionName = this.$route.params.stallPosition
      }

      if (this.$route.params.selectedPeople) {
        console.log('xuyao ', this.$route.params.selectedPeople);
        this.remindPeople = ''
        this.remindPeopleArr = this.$route.params.selectedPeople;

        for (var i = 0; i < this.remindPeopleArr.length; i++) {

          if (i != 0) {
            this.remindPeople += ',' + this.remindPeopleArr[i].showName
          } else {
            this.remindPeople += this.remindPeopleArr[i].showName
          }
        }

      }
      this.positionName = this.MCRM_GDPosition.address

      if (this.MCRM_GDPosition.address == '') {
        this.getNowAddress()
      }
      this.opId = this.MCRM_opId


    },
    mounted(){


      this.originFontSize = window.getComputedStyle(document.documentElement).fontSize;
      document.documentElement.style.fontSize = document.documentElement.clientWidth / 414 * 100 + 'px';


      console.log("ok2", window.location.href);
    },
//    过滤方法
    filters: {},
    destroyed(){
      document.documentElement.style.fontSize = parseInt(this.originFontSize) + 'px';
    },
    methods: {
      ...mapMutations({
          setGDPosition: 'SET_GDPosition'
        }
      ),
      //      定位
      getNowAddress(){
        Indicator.open('加载中...')
        MapLoader().then(AMap => {
          getLocation().then(localPosition => {
            Indicator.close('加载中...')
            console.log(localPosition)
            this.positionName = localPosition.formattedAddress;

            this.setGDPosition({
              lat: localPosition.position.lat,
              lng: localPosition.position.lng,
              address: localPosition.formattedAddress
            },)


          }).catch(res => {
            Indicator.close('加载中...')
            console.log(res)
          })
        }, e => {
          Indicator.close('加载中...')
          console.log('地图加载失败', e)
        })
      },


//        跳转地址更换
      changePosition(){
        this.$router.push({path: 'ChangePosition'})
      },


      //   删除照片
      imgClose(index){
        this.imageList.splice(index, 1)
        if (this.imageList.length < 3) {
          this.showAddImages = true
        }
      },
//      展示预览照片
      showPreview(index){
        this.imgIndex = parseInt(index)
        console.log('ok预览');
        this.showImgPreview = true
      },
//      取消预览照片
      cancelPreview(){
        this.showImgPreview = false
      },
//      调用拍照

      takePhoto(){
        objName.upload();
        /* var self = this
         this.convertImgToBase64(this.baseSrc, function(base64Img){
         //转化后的base64
         console.log(base64Img);
         self.imageList.push(base64Img)
         if (self.imageList.length >= 3) {
         self.showAddImages = false
         }
         console.log(base64Img);
         });*/
      },
      setImag(path){
        this.sheetVisible = false
        this.imageList.push(path)
        if (this.imageList.length >= 3) {
          this.showAddImages = false
        }

      },


      changeSwitch(){
        console.log(this.switchSign);
      },
//        跳转
      backBussiness(){
        this.$router.push({name: 'StallDetail'});
      },
      toRemindPeople(){
        if (this.remindPeopleArr !== '' || this.remindPeopleArr !== null) {
          this.$router.push({name: 'RemindSomeBody', params: {"remindPeopleArr": this.remindPeopleArr}});
        }
        this.$router.push({name: 'RemindSomeBody'});
      },
//      调用手机相机
      addImages(){
        this.sheetVisible = true
        this.actions = [{
          name: '拍照',
          method: this.takePhoto
        },
          /* {
           name: '从相册中选择',
           method: this.takePhoto
           }*/
        ];
      },
//      完成
      sendBtn(){
        let stallId = this.MCRM_stallId
        let latitude = this.MCRM_GDPosition.lat
        let longitude = this.MCRM_GDPosition.lng
        let locationName = encodeURIComponent(this.MCRM_GDPosition.address)
        let opId = this.MCRM_opId
        let orgId = this.MCRM_orgId
        let showName = encodeURIComponent(this.MCRM_loginName)
        let text = encodeURIComponent(this.inputText)

        if (text == '') {
          MessageBox('提示', '请填写发布内容')
          return
        }

        if (latitude == '' || longitude == '' || locationName == '') {
          MessageBox('提示', '网络超时，暂时无法获取地址信息，请检查网络设置或稍后重试')
          return
        }

        let imgArr = this.imageList
        let imgArrNew = []
        for (var i = 0; i < 3; i++) {
          if (imgArr[i]) {
            imgArrNew.push(imgArr[i])
          } else {
            imgArrNew.push('')
          }
        }
        let img1 = imgArrNew[0]
        let img2 = imgArrNew[1]
        let img3 = imgArrNew[2]
        let remindlist = this.remindPeopleArr

        Indicator.open('加载中...')
        this.MCRM_ajax_sendWorkCircle(stallId, latitude, locationName, longitude, opId, orgId, showName, text, img1, img2, img3, remindlist).then(res => {
          console.log('catch res:', res);
          Indicator.close('加载中...')
          if (res.msg.code == 1) {
            this.$router.push({name: 'StallDetail'})
          } else {
            MessageBox('提示', res.msg.message)
          }

        }).catch(res => {

          MessageBox('提示', '网络超时')
          setTimeout(() => {
            Indicator.close('加载中...')
          }, 10)

        })
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang='less'>
  .stallAddImgPage {
    .checkDisabled {
      .mint-switch-input:checked + .mint-switch-core {
        background-color: rgba(235, 235, 235, 1);
        border-color: rgba(235, 235, 235, 1)
      }
    ;
    }
    .mint-switch-input:checked + .mint-switch-core::after {
      transform: translateX(.2rem);
    }

    .mint-switch-core {
      width: .46rem;
      height: .24rem;
      border-radius: .12rem;
    }
    .mint-switch-core::before {
      width: .44rem;
      height: .22rem;
      border-radius: .11rem;
    }
    .mint-switch-core::after {
      width: .22rem;
      height: .22rem;
      border-radius: .11rem;
    }

  }

</style>
<style lang="less" scoped>

  @borderColor: #e1e1e1;

  .stallAddImgPage {
    font-size: .14rem;

    .imgPreview {
      position: fixed;
      top: 0;
      bottom: 0;
      width: 100%;
      z-index: 1000;
      background-color: #000;
      .mint-swipe-items-wrap {
        position: relative;
      }
      img {
        position: absolute;
        top: 0;
        bottom: 0;
        margin: auto;
      }
    }
    .stallAddImg {
      position: fixed;
      top: .52rem;
      bottom: 0;
      width: 100%;
      height: auto;
      overflow-y: auto;
      .stallAddImgContent {
        textarea {
          width: 100%;
          height: 1.69rem;
          outline: none;
          resize: none;
          font-size: .16rem;
          border: none;
          &::-webkit-input-placeholder {
            color: #969696;
          }
          &::-moz-placeholder { //不知道为何火狐的placeholder的颜色是粉红色，怎么改都不行，希望有大牛路过帮忙指点
            color: #969696;
          }
          &:-ms-input-placeholder { //由于我的IE刚好是IE9，支持不了placeholder，所以也测试不了(⊙﹏⊙)，有IE10以上的娃可以帮我试试
            color: #969696;
          }
        }
        .imgEdit {
          width: 100%;
          overflow: hidden;
          & > div {
            float: left;
          }
          .imgPage {
            background-color: #e4f2f3;
            width: .8rem;
            height: .8rem;
            padding: .05rem;
            box-shadow: .04rem .06rem .2rem rgba(196, 250, 234, 1);
            margin: 0 .1rem .1rem 0;
            position: relative;
            & > div {
              width: 100%;
              height: 100%;
              overflow: hidden;
            }
            img {
              width: 100%;

            }
            .imgClose {
              position: absolute;
              top: 0;
              right: 0;
              background: url("../../../assets/images/retail/deleteImage.svg");
              background-size: 100% 100%;
              width: .2rem;
              height: .2rem;
            }
          }
          .addImgBtn {
            background: url("../../../assets/images/retail/addImage.svg");
            background-size: 100% 100%;
            box-shadow: none;
            margin: 0;
            padding: 0;
            width: .9rem;
            height: .9rem;
          }

        }

        padding: .17rem;
      }

    }

    .listTable {
      padding: .14rem .09rem;
      border-bottom: 1px solid #EAEAEA;

      .tableLabel {
        font-size: .14rem;
        float: left;
        width: .9rem;
        height: .24rem;
        line-height: .24rem;
      }
      .tableContent {
        height: .24rem;
        line-height: .24rem;
        text-align: right;
        padding-left: .9rem;
        position: relative;
        .addPartner {
          position: absolute;
          top: 50%;
          right: 0;
          background: url("../../../assets/images/stall/arrow-right.svg");
          background-size: 100% 100%;
          width: .15rem;
          height: .15rem;
          margin-top: -0.075rem;

        }
        .stallText {
          color: #909090;
          padding-right: .2rem;
          overflow: hidden;
          text-overflow: ellipsis;
          white-space: nowrap;
        }
        .stallSwitch {
          float: right;

        }

      }

    }

  }


</style>
