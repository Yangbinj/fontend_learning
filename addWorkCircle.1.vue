<template>
  <div class="stallAddImgPage">
    <!--弹窗部分-->
    <mt-actionsheet :actions="actions" v-model="sheetVisible">
    </mt-actionsheet>
    <!--弹窗部分end-->

    <!--图片预览展示-->
    <!-- <div class="imgPreview" v-show="showImgPreview">
      <mt-swipe :auto="0" :defaultIndex="imgIndex" :continuous="false">
        <mt-swipe-item v-for="(item,index) in imageList" :key="index" @click.native.prevent.stop="cancelPreview">
          <img :src=" 'data:image/jpeg;base64,'+ item" alt="" width="100%">
        </mt-swipe-item>
      </mt-swipe>
      

    </div> -->

    <!--图片预览展示end-->
    <vm-nav-bar class="mcrm-navBar" :title="title" left-arrow :right-text="right" @click-left="backBussiness" @click-right="sendBtn" />

    <section class="stallAddImg ios_mescroll">
      <!-- <vm-popup v-model="showPop" :overlay="overlay" style="height:30%;width:80%" >内容</vm-popup> -->
      <vm-popup v-model="showPop" :overlay-style="overlayStyle" style="height:auto;width:30%;overflow:hidden;border-radius:10px;">
        <div class="sybCustomeraddway">
          <ul>
            <li @click.prevent.stop="takePhotoVue()"><img src="../../assets/images/circle/ICON-114.svg">
              <p>手机拍照</p>
            </li>
            <!-- <li id="quchu" @click.prevent.stop="photoAddVue()"><img src="../../assets/images/circle/ICON-115.svg">
              <p>相册添加</p>
            </li> -->
          </ul>
        </div>
      </vm-popup>
      <div class="stallAddImgContent">
        <!-- <textarea placeholder="请输入发布内容" v-model="inputText"> </textarea> -->
        <vm-field v-model="inputText" type="textarea" placeholder="请输入发布内容" rows="10" />
        <div class="imgEdit">
          <div class="imgPage" v-for="(item,index) in imageList" :key="index" @click.prevent.stop="imagePreviewV(index)">
            <div class="imgClose" @click.prevent.stop="imgClose(index)"></div>
            <div @click.prevent.stop="imagePreviewV">
              <img :src="'data:image/jpeg;base64,'+item" alt="" >
              <!-- <img :src="item" alt="" @click.prevent.stop="imagePreview(index)"> -->
                
            </div>

          </div>

          <div class="addImgBtn" v-show="showAddImages" @click.prevent.stop="addImages"></div>
        </div>
        <div class="listTable">
          <div class="tableLabel">是否为签到</div>
          <div class="tableContent">
            <div :class="[isSign ? 'checkDisabled':'', 'stallSwitch']">
              <!-- <vm-switch v-model="switchSign" /> -->
              <mt-switch v-model="switchSign" @change="changeSwitch" :disabled='isSign'></mt-switch>
            </div>

          </div>
        </div>
        <div v-show='switchSign' class="listTable" @click.prevent.stop="changePosition">
          <div class="tableLabel">所在位置</div>
          <div class="tableContent">
            <div class="addPartner"></div>

            <div class="stallText">{{positionName}}</div>

          </div>
        </div>
        <div class="listTable" @click.prevent.stop="toRemindPeople">
          <div class="tableLabel">提醒谁看</div>
          <div class="tableContent">
            <div class="addPartner"></div>
            <div class="stallText">{{remindPeople}}</div>

          </div>
        </div>

      </div>

      <div class="deleteTeamButton-css">
        <vm-button block size="lg" class="bg-blue" @click.prevent.stop="sendBtn">完成</vm-button>
      </div>
    </section>

  </div>
</template>

<script>
import { Indicator } from 'mint-ui';
import { MessageBox } from 'mint-ui';

//  import {test} from '../../assets/js/test'
//  import {getPicture} from '../../assets/js/cameraGetPicture'
import { MapLoader, getLocation } from '../../utils/Amap';

import { mapGetters, mapMutations } from 'vuex';

export default {
  //    name: 'RetailOrderDetails',
  data() {
    return {
      title: '发布朋友圈',
      showPop: false,
      inputText: this.MCRM_pushWorkCircleContent,
      switchSign: false,
      overlayStyle: { background: '#EEEEEE', opacity: '0.4' },
      isSign: false,
      overlay: false,
      positionName: '',
      isStall: true,
      remindPeople: '提醒人员',
      switchStall: true,
      sheetVisible: false,
      fasdfasdImage:['../../assets/images/circle/ICON-114.svg','../../assets/images/circle/ICON-114.svg','../../assets/images/circle/ICON-114.svg'],
      actions: [],
      imageList: ['1','1','1'],
      imgIndex: 0,
      showAddImages: true,
      showImgPreview: false,
      remindPeopleArr: null,
      /* baseSrc: '/static/shopEdit.svg',
         baseSrcNew: ''*/
    };
  },
  computed: {
    //        获取OPId
    ...mapGetters([
      'MCRM_opId',
      'MCRM_orgId',
      'MCRM_loginName',
      'MCRM_stallId',
      'MCRM_GDPosition',
      'MCRM_pushWorkCircleContent',
      'MCRM_pushWorkCircleIsSigned',
      'MCRM_pushWorkCircleImages',
    ]),
    //  inputText: function() {
    //        this.SET_pushWorkCircleContent=this.inputText
    //         console.log('this.SET_pushWorkCircleContent',this.SET_pushWorkCircleContent)
    //         }
  },
  watch: {
    inputText: function(newval, oldval) {
      // console.log(newval,oldval);
      this.SETpushWorkCircleContent(newval);

      console.log(
        'this.SET_pushWorkCircleContent',
        this.MCRM_pushWorkCircleContent,
      );
    },
      imageList: function(newval, oldval) {
      // console.log(newval,oldval);
      // this.SET_pushWorkCircleImages(newval);
      this.SET_pushWorkCircleImages(newval);
      // this.imageList=this.MCRM_pushWorkCircleImages
      console.log(
        'this.SET_pushWorkCircleContent',
        this.MCRM_pushWorkCircleImages,
      );
    },
  },
  created() {
    document.title = this.title;
    window.setImag = this.setImag;
    console.log(
      'this.$route.params.stallPosition ',
      this.$route.params.stallPosition,
    );
    this.inputText = this.MCRM_pushWorkCircleContent;
    this.switchSign = this.MCRM_pushWorkCircleIsSigned;
    this.imageList=this.MCRM_pushWorkCircleImages
    console.log(
      'this.$route.params.stallPosition ',
      this.MCRM_pushWorkCircleContent,
    );
    if (this.$route.params.stallPosition) {
      this.positionName = this.$route.params.stallPosition;
      this.switchSign = true;
      console.log(
        '------------------>stallPostion<----------',
        JSON.stringify(this.positionName),
      );
    }

    if (this.$route.params.selectedPeople) {
      console.log('xuyao ', this.$route.params.selectedPeople);
      this.remindPeople = '';
      this.remindPeopleArr = this.$route.params.selectedPeople;

      for (var i = 0; i < this.remindPeopleArr.length; i++) {
        if (i != 0) {
          this.remindPeople += ',' + this.remindPeopleArr[i].showName;
        } else {
          this.remindPeople += this.remindPeopleArr[i].showName;
        }
      }
    }
    this.positionName = this.MCRM_GDPosition.address;

    if (this.MCRM_GDPosition.address == '') {
      // this.getNowAddress()
    }
    this.opId = this.MCRM_opId;
      console.log(
      'MCRM_pushWorkCircleImages ',
      this.MCRM_pushWorkCircleImages,
    );
  },
  mounted() {
    console.log('ok2', window.location.href);
  },
  //    过滤方法
  filters: {},
  destroyed() {
    document.documentElement.style.fontSize =
      parseInt(this.originFontSize) + 'px';
  },
  methods: {
     imagePreviewV(index){
                var array = [];
                // for (var i = 0; i < this.imgList.length; i++) {
                for (var i = 0; i < 3; i++) {
                    array[i] = 'data:image/jpeg;base64,' + this.imgList[i];
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
    ...mapMutations({
      setStallId: 'SET_stallId',
      setSession: 'SET_session',
      setOpId: 'SET_OPID',
      setOrgId: 'SET_ORGID',
      SETTmpTeamInfo: 'SET_tmpTeamInfo',
      SETpushWorkCircleContent: 'SET_pushWorkCircleContent',
      SETpushWorkCircleIsSigned: 'SET_pushWorkCircleIsSigned',
      SET_pushWorkCircleImages:'SET_pushWorkCircleImages'
    }),
    onChange(files, type, index) {
      this.files = files;
      if (type === 'add') {
        this.$toast(type);
      } else {
        this.$toast(type + ' index:' + index);
      }
    },

    onImageClick(files, index) {
      this.$toast(`点击了第${index}张图片`);
    },

    ...mapMutations({
      setGDPosition: 'SET_GDPosition',
    }),
    //      定位
    getNowAddress() {
      Indicator.open('加载中...');
      MapLoader().then(
        AMap => {
          getLocation()
            .then(localPosition => {
              Indicator.close('加载中...');
              console.log(localPosition);
              this.positionName = localPosition.formattedAddress;

              this.setGDPosition({
                lat: localPosition.position.lat,
                lng: localPosition.position.lng,
                address: localPosition.formattedAddress,
              });
            })
            .catch(res => {
              Indicator.close('加载中...');
              console.log(res);
            });
        },
        e => {
          Indicator.close('加载中...');
          console.log('地图加载失败', e);
        },
      );
    },

    //        跳转地址更换
    changePosition() {
      this.$router.push({ path: 'ChangePosition' });
    },
    takePhotoVue() {
      window.AITakePhotoCallback = this.AITakePhotoCallback;
      this.sendPostMessage('AITakePhoto', { isBorder: 0 }); //isBorder：0：系统相机，1：现场照相机
    },
    AITakePhotoCallback(ImageByte){
       var self = this;
      if (self.isIos()) {
        ImageByte = JSON.parse(ImageByte);
      }
      this.showPop=false;
      // if(self.imageList.length<3){
        self.imageList.push(ImageByte.base64)
        if(self.imageList.length===3){
          self.showAddImages=false
        }
        self.SET_pushWorkCircleImages(self.imageList)
        
      console.log("ImageByte",JSON.stringify(ImageByte))
    },
    photoAddVue() {},

    //   删除照片
    imgClose(index) {
      this.imageList.splice(index, 1);
      if (this.imageList.length < 3) {
        this.showAddImages = true;
      }
    },
    //      展示预览照片
    showPreview(index) {
      this.imgIndex = parseInt(index);
      console.log('ok预览');
      this.showImgPreview = true;
    },
    //      取消预览照片
    cancelPreview() {
      this.showImgPreview = false;
    },
    //      调用拍照

    takePhoto() {
      objName.upload();
    },
    setImag(path) {
      this.sheetVisible = false;
      this.imageList.push(path);
      if (this.imageList.length >= 3) {
        this.showAddImages = false;
      }
    },

    changeSwitch() {
      console.log(this.switchSign);
      this.SETpushWorkCircleIsSigned(this.switchSign);
      console.log(
        'SET_pushWorkCircleIsSigned',
        this.MCRM_pushWorkCircleIsSigned,
      );
      if (this.switchSign == true) {
        this.positionName = this.MCRM_GDPosition.address;
        if (this.MCRM_GDPosition.address == '') {
          this.getNowAddress();
        }
      }
    },
    //        跳转
    backBussiness() {
      this.$router.push({ name: 'workcircle' });
    },
    toRemindPeople() {
      if (this.remindPeopleArr !== '' || this.remindPeopleArr !== null) {
        this.$router.push({
          name: 'remindSomeBody',
          params: { remindPeopleArr: this.remindPeopleArr },
        });
      }
      this.$router.push({ name: 'remindSomeBody' });
    },
    //      调用手机相机
    addImages() {
      this.showPop = true;
      // this.sheetVisible = true;
      // this.actions = [
      //   {
      //     name: '拍照',
      //     method: this.takePhoto,
      //   },
      // ];
    },
    //      完成
    sendBtn() {
      let stallId = this.MCRM_stallId;
      let latitude = this.MCRM_GDPosition.lat;
      let longitude = this.MCRM_GDPosition.lng;
      let locationName = encodeURIComponent(this.MCRM_GDPosition.address);
      let opId = this.MCRM_opId;
      let orgId = this.MCRM_orgId;
      let showName = encodeURIComponent(this.MCRM_loginName);
      let text = encodeURIComponent(this.inputText);

      if (text == '') {
        MessageBox('提示', '请填写发布内容');
        return;
      }

      // if (latitude == '' || longitude == '' || locationName == '') {
      //   MessageBox('提示', '网络超时，暂时无法获取地址信息，请检查网络设置或稍后重试')
      //   return
      // }

      let imgArr = this.imageList;
      let imgArrNew = [];
      for (var i = 0; i < 3; i++) {
        if (imgArr[i]) {
          imgArrNew.push(imgArr[i]);
        } else {
          imgArrNew.push('');
        }
      }
      let img1 = imgArrNew[0];
      let img2 = imgArrNew[1];
      let img3 = imgArrNew[2];
      let remindlist = this.remindPeopleArr;
      console.log('remindList:', remindlist);

      Indicator.open('加载中...');
      this.MCRM_ajax_sendWorkCircle(
        stallId,
        latitude,
        locationName,
        longitude,
        opId,
        orgId,
        showName,
        text,
        img1,
        img2,
        img3,
        remindlist,
      )
        .then(res => {
          console.log('catch res:', res);
          Indicator.close('加载中...');
          if (res.msg.code == 1) {
            this.$router.push({ name: 'workcircle' });
          } else {
            MessageBox('提示', res.msg.message);
          }
        })
        .catch(res => {
          MessageBox('提示', '网络超时');
          setTimeout(() => {
            Indicator.close('加载中...');
          }, 10);
        });
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang='less'>
.sybCustomeraddway {
  border-radius: 10px;
  ul {
    padding: 3px 1px;
  }
  li {
    text-align: center;
    width: 100%;
    min-width: 60px;
    float: left;
    border-right: 1px solid #eeeeee;
    &:last-of-type {
      border-right: none;
    }
  }
  img {
    height: 30px; //30px;
    width: auto;
    margin-bottom: 5px;
  }
}

.deleteTeamButton-css {
  padding: 10px;
}
.stallAddImgPage {
  .checkDisabled {
    .mint-switch-input:checked + .mint-switch-core {
      background-color: rgba(235, 235, 235, 1);
      border-color: rgba(235, 235, 235, 1);
    }
  }
  .mint-switch-input:checked + .mint-switch-core::after {
    transform: translateX(20px);
  }

  .mint-switch-core {
    width: 46px;
    height: 24px;
    border-radius: 12px;
  }
  .mint-switch-core::before {
    width: 44px;
    height: 22px;
    border-radius: 11px;
  }
  .mint-switch-core::after {
    width: 22px;
    height: 22px;
    border-radius: 11px;
  }
}
</style>
<style lang="less" type="text/less">
@borderColor: #e1e1e1;

.stallAddImgPage {
  font-size: 14px;

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
    top: 52px;
    bottom: 0;
    width: 100%;
    height: auto;
    overflow-y: auto;
    .stallAddImgContent {
      textarea {
        width: 100%;
        height: 169px;
        outline: none;
        resize: none;
        font-size: 16px;
        border: none;
        &::-webkit-input-placeholder {
          color: #969696;
        }
        &::-moz-placeholder {
          //不知道为何火狐的placeholder的颜色是粉红色，怎么改都不行，希望有大牛路过帮忙指点
          color: #969696;
        }
        &:-ms-input-placeholder {
          //由于我的IE刚好是IE9，支持不了placeholder，所以也测试不了(⊙﹏⊙)，有IE10以上的娃可以帮我试试
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
          width: 80px;
          height: 80px;
          padding: 5px;
          box-shadow: 4px 6px 20px rgba(196, 250, 234, 1);
          margin: 0 10px 10px 0;
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
            background: url('../../assets/images/retail/deleteImage.svg');
            background-size: 100% 100%;
            width: 20px;
            height: 20px;
          }
        }
        .addImgBtn {
          background: url('../../assets/images/retail/addImage.svg');
          background-size: 100% 100%;
          box-shadow: none;
          margin: 0;
          padding: 0;
          width: 90px;
          height: 90px;
        }
      }

      padding: 17px;
    }
  }

  .listTable {
    padding: 14px 9px;
    border-bottom: 1px solid #eaeaea;

    .tableLabel {
      font-size: 14px;
      float: left;
      width: 90px;
      height: 24px;
      line-height: 24px;
    }
    .tableContent {
      height: 24px;
      line-height: 24px;
      text-align: right;
      padding-left: 9px;
      position: relative;
      .addPartner {
        position: absolute;
        top: 50%;
        right: 0;
        background: url('../../assets/images/stall/arrow-right.svg');
        background-size: 100% 100%;
        width: 15px;
        height: 15px;
        margin-top: -7.5px;
      }
      .stallText {
        color: #909090;
        padding-right: 20px;
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
