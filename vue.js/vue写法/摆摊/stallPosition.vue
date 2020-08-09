<template>
  <div class="retailStallPositionPage">

    <mt-header title="摆摊选址">
      <mt-button slot="left" @click.prevent.stop="backBussiness"><span class="icon-goBack"></span></mt-button>
      <!--<mt-button slot="right" @click.prevent.stop="backBussiness"><span class="icon-headerSure"></span></mt-button>-->
    </mt-header>
    <div class="searchPart positionSearch">
      <div class="search">
        <!-- <mt-search
           v-model="value"
           cancel-text="取消"
           placeholder="请输入摆摊地点搜索…" @input.native="searchPosition" @blur="closeSearch" @focus.native="closeSearch"
           @change.native="closeSearch">
         </mt-search>-->
        <!--mintUI search组件 改写-->
        <div class="mint-search">
          <div class="mint-searchbar">
            <div class="mint-searchbar-inner">
              <i class="mintui mintui-search"></i>
              <input ref="input" v-model="value" type="search" placeholder="请输入摆摊地点搜索…" class="mint-searchbar-core"
                     @input="searchPosition" @focus="searchOn">
            </div>
            <a class="mint-searchbar-cancel" v-show="inputVisible" @click="searchCancel">取消</a>

          </div>

        </div>
        <div class="searchBtn">
          <a class="mint-searchbar-cancel " v-show="true" @click="searechPotentialCustomer">搜索</a>
        </div>


      </div>
      <div class="searchList" v-if="showSearchList">
        <ul>
          <li v-for="item in searchList" @click.prevent.stop="positionSelected(item.name,item.location)"><p>{{item.name}}</p>
            <p>{{item.address}}</p></li>
        </ul>
      </div>
    </div>

    <div class="nav">
      <ul>
        <li v-for="(item,index) in liLists" @click.prevent.stop="navClick(item.selected,index)"
            :class="{'selected':item.selected}">{{item.name}}
        </li>
      </ul>
    </div>

    <!--mescroll-->
    <div class="mescroll ios_mescroll_position">

      <div class="content">
        <div class="rangeModule">
          <ul>
            <li style="position: relative" @click.prevent.stop="IsShowRangeNum(showRangeNum)">附近{{rangeNum}}<span
              :class="[showRangeNum ?'iconTriUP':'iconTriDown']"></span></li>
            <li v-show="showRangeNum" @click.prevent.stop="rangeNumSelected(index)" v-for="(item,index) in  rangeNums">
              {{item.name}}
            </li>

          </ul>
        </div>
        <div v-show="showSearchList" style="position: absolute;top: 0;width: 100%;bottom: 0;z-index: 9"
             @click.prevent.stop="showSearchList=false"></div>

        <div id="container"></div>


      </div>

    </div>
    <div class="footer">
      <p>周围{{this.rangeNum}}潜客数{{customerType}}</p>
      <div class="btn">
        <mt-button type="primary" @click.prevent.stop="pullstall">摆摊</mt-button>
      </div>
    </div>


  </div>
</template>

<script>
  //  import
  import {MapLoader, GDtoBD} from '../../../assets/js/Amap'
  import {Indicator} from 'mint-ui'
  import {MessageBox} from 'mint-ui'
  import {mapGetters} from 'vuex'
  export default {
    name: '',
    computed:{
      ...mapGetters([
        'MCRM_opId',
        'MCRM_orgId'
      ])
    },
    data () {
      return {
        liLists: [
          {name: '宽带融合', selected: true, type: 500011},
          {name: '终端合约', selected: false, type: 500081},
          {name: '两网优惠', selected: false, type: 500020},
          {name: '流量优惠', selected: false, type: 500032},
          {name: '话费预缴', selected: false, type: 500061},
          {name: '月费预缴', selected: false, type: 500062},
        ],
        navSelected: '500011',
        value: '',
        cityinfo: '',
        cityCode: '',
        searchList: [],//搜索列表
        showSearchList: false,
        inputVisible: false,
        rangeNum: '1km',
        rangNumNew: 1000,
        rangeNums: [
          {name: '200m', value: '200'},
          {name: '500m', value: '500'},
          {name: '1km', value: '1000'},
          {name: '1.5km', value: '1500'},
          {name: '2km', value: '2000'}
        ],
        showRangeNum: false,
        map: null,
        location: {
          lng: '',
          lat: ''
        },
        customerNum: 0,
        customerType: '',
        searchLocation: {
          lng: '',
          lat: ''
        }
      }
    },
    created(){

    },
    /* activated() {
     setTimeout(() => {
     console.log('okok');
     let top = parseInt(this.localSaveGlobal.getStore('orderManagerScroll'))
     this.mescroll.setScrollTop(top)
     }, 20);

     },*/
    deactivated(){

    },


    mounted(){
      var self = this
      /*    document.getElementById("app").addEventListener('click',function () {
       console.log('ok');
       self.showSearchList = false
       })*/
      var map;
      this.pageSizeReset();
//      Indicator.open('加载中...')

//      let that = this
      var self = this
      MapLoader().then(AMap => {
        self.AMap = AMap
        console.log('地图加载成功')
//移除高德logo点击跳转
        $(".amap-logo").on("click", function (e) {
          e.preventDefault();
          return false;
        });
//地图初始化
        self.map = map = new AMap.Map('container', {
          resizeEnable: true,
          zoom: 16
        });

//地图搜索

//        self.map = map;
//        self.range = "500";

//地图点击事件
        map.on('click', function (e) {
          self.showSearchList = false

          console.log('位置', e);
          if (typeof(self.searchMarker) == "undefined") {
            self.searchMarker = new AMap.Marker({
              position: [e.lnglat.getLng(), e.lnglat.getLat()],
//              draggable:true
            });
          } else {
            self.searchMarker.setPosition([e.lnglat.getLng(), e.lnglat.getLat()]);
          }
          self.location.lng = e.lnglat.getLng()
          self.location.lat = e.lnglat.getLat()
//          self.lng = e.lnglat.getLng();
//          self.lat = e.lnglat.getLat();
          self.searchMarker.setMap(map);
          var geocoder = new AMap.Geocoder({
            radius: 1000,
            extensions: "all"
          });
          geocoder.getAddress([e.lnglat.getLng(), e.lnglat.getLat()], function (status, result) {
            if (status === 'complete' && result.info === 'OK') {
              var address = result.regeocode.formattedAddress; //返回地址描述
              self.value = address;
            }
          });
        });

//地图定位功能开启
        AMap.plugin('AMap.Geolocation', function () {
          function onComplete(data) {
            Indicator.close('加载中...')
//            self.cityinfo = data.addressComponent.city;
            self.cityCode = data.addressComponent.citycode;

            console.log('城市', self.cityinfo)
            console.log('定位成功', data)

            if (typeof(self.searchMarker) == "undefined") {
              self.searchMarker = new AMap.Marker({
                position: [data.position.getLng(), data.position.getLat()],
//                draggable: true
              });
            }
            else {
              self.searchMarker.setPosition([data.position.getLng(), data.position.getLat()]);
            }
            self.searchMarker.setMap(map);
            self.value = data.formattedAddress
            self.location.lat = data.position.getLat();
            self.location.lng = data.position.getLng();
//            console.log(data.formattedAddress);
//            self.lat = data.position.getLat();
//            self.lng = data.position.getLng();
//			    	centerMarker.on("dragend", function(e){
////			    		$("#longitude").val(e.point.lng);
////	               	 	$("#latitude").val(e.point.lat);
//			    		alert(123);
//	                });
          }

          function onError(data) {
//            Indicator.close('加载中...')
            console.log('定位失败', data)
          }

          var geolocation = new AMap.Geolocation({
            enableHighAccuracy: true,//是否使用高精度定位，默认:true
            timeout: 10000,          //超过10秒后停止定位，默认：无穷大
            maximumAge: 0,           //定位结果缓存0毫秒，默认：0
//			            buttonOffset: new AMap.Pixel(10, 20),//定位按钮与设置的停靠位置的偏移量，默认：Pixel(10, 20)
            zoomToAccuracy: false,      //定位成功后调整地图视野范围使定位位置及精度范围视野内可见，默认：false
            buttonPosition: 'RB',
//            useNative: true//是否使用高德定位sdk用来辅助优化定位效果，默认：false
          });
          map.addControl(geolocation);
          geolocation.getCurrentPosition();
          AMap.event.addListener(geolocation, 'complete', onComplete);//返回定位信息
          AMap.event.addListener(geolocation, 'error', onError);      //返回定位出错信息
        });

      }, e => {
        console.log('地图加载失败', e)
      })

      console.log("ok", window.location.href);


    },
//    组件销毁事件
    destroyed(){
//      document.documentElement.style.fontSize = parseInt(this.originFontSize) + 'px';
    },
//    过滤器
    filters: {},
    beforeRouteLeave(to, from, next) {
      // 设置下一个路由的 meta
//      to.meta.keepAlive = true; // 让 A 缓存，即不刷新
      next();
    },
//    事件方法
    methods: {
//        搜索框事件
      searchCancel(){
        this.inputVisible = false
        this.showSearchList = false
        this.value = ''
        this.$refs.input.blur()
      },
//      搜索框点击事件
      searchOn(){
        this.inputVisible = true
      },
      searchGo(){
        console.log('search 潜客')
      },
//      搜索列表点击事件
      positionSelected(name, position){

        var self = this;
        self.showSearchList = false
        self.value = name

        if (typeof(self.searchMarker) != "undefined") {
          self.searchMarker.setMap(null);
        }
        self.map.setCenter([position.lng, position.lat]);
        self.searchMarker = new self.AMap.Marker({
          position: [position.lng, position.lat],
          map: self.map,
        });

      },


//        search搜索事件
      searchPosition(){
        console.log('search....');
        var self = this
//精确地市
        if (typeof(self.cityinfo) == "undefined" || self.cityinfo == "") {
          var citysearch = new AMap.CitySearch();
          citysearch.getLocalCity(function (status, result) {
            console.log('this is my want:', status, result);

            if (status === 'complete' && result.info === 'OK') {
              if (result && result.city && result.bounds) {

                var cityinfo = result.city;
                self.cityinfo = cityinfo;
              }
            }
          });
        }


//搜索位置的信息
        var parameters = {};
        this.AMap.service(["AMap.PlaceSearch"], function () {
//            创建位置搜索
          var placeSearch = new AMap.PlaceSearch({ //构造地点查询类
            pageSize: 10,
            pageIndex: 1,
            city: self.cityinfo, //城市
//		            citylimit:true,
          });
          //关键字查询
          placeSearch.search(self.value, function (status, result) {
            if (result && result.poiList && result.poiList.pois) {
              parameters = result.poiList.pois;//加载筛选数据
              self.searchList = parameters
              console.log(parameters)
            }

            if (self.value.length <= 0) {
              self.showSearchList = false
              self.searchList = []
            } else {
              self.showSearchList = true
            }
          });
        });


      },

//      range 范围选择事件
      rangeNumSelected(index){
        this.rangeNum = this.rangeNums[index].name
        this.rangNumNew = parseInt(this.rangeNums[index].value)
        this.showRangeNum = false
      },
      IsShowRangeNum(showRangeNum){
        this.showRangeNum = !showRangeNum
      },

//导航栏点击事件
      navClick(status, index){
//        this.navSelected = index.toString()
        status = !status
        this.liLists[index].selected = status
        this.navSelected = ''
        for (let i = 0; i < this.liLists.length; i++) {
          if (this.liLists[i].selected) {
            if (this.navSelected == '') {
              this.navSelected += this.liLists[i].type
            } else {
              this.navSelected += ',' + this.liLists[i].type
            }

          }

        }
      },
//      字体初始化
      pageSizeReset(){
        this.originFontSize = window.getComputedStyle(document.documentElement).fontSize;
        document.documentElement.style.fontSize = document.documentElement.clientWidth / 414 * 100 + 'px';
      },
//      潜客搜索事件
      searechPotentialCustomer(){

        //潜客数量接口有问题，
        var self = this;
        var rangeNum = self.range;
        var disRangeNum = "1km";//默认
        console.log(self.location.lng, self.location.lat)
        var baiduAddr = GDtoBD(self.location.lng, self.location.lat);
        var isStall = "";
//        var comefrom = ZJMCCMobile.utils.getParam("nearbyCustomersNewComeFrom");
//        if (comefrom == "putStallNewPage.html") {
        isStall = "1";//1查询摆摊潜客，否则查询普通潜客

//        }

        if (self.navSelected == "") {
          MessageBox('提示', "请选择至少一个二级类型");
          return;
        }
//			var baiduAddr = GDtoBD(self.lng,self.lat);
        var baiduLat = baiduAddr[1];
        baiduLat = baiduLat.toFixed(6);
//			self.lat = baiduLat;
//
        var baiduLng = baiduAddr[0];
        baiduLng = baiduLng.toFixed(6);
//			self.lng = baiduLng;

        //组织编号

        var orgId = this.MCRM_orgId;
        var opId = this.MCRM_opId
        //操作员编号

        var params = [opId, orgId, baiduLat, baiduLng, self.type, rangeNum, isStall];
//        显示搜索等待标识
        Indicator.open('搜索中...');
//        self.showNearBySearchDialog();//        addOneAPMEvent("PotentialCustomer", "searchkey:" + self.type, "searchValue:" + self.type);
        this.MCRM_ajax_queryPotentialCustomers(opId, orgId, baiduLat, baiduLng, self.navSelected, self.rangNumNew, "1").then((res) => {
          Indicator.close('搜索中...');
          if (res.msg.code == "1") {
            var data = res.data;
            self.customerNum = data.TOTAL_COUNT;

            //记录搜索潜客时的经纬度
            self.searchLocation.lat = self.location.lat;
            self.searchLocation.lng = self.location.lng;


            self.customerType = ''
            if (self.customerNum == 0) {
              self.customerType = '有0个潜客'
              return;
            }
//            self.typetxt = "";
            for (var i = 0; i < data.DETAIL_COUNT.length; i++) {
              /* if (i != 0) {
               self.typetxt += ",";
               }
               self.typetxt += data.DETAIL_COUNT[i].PLAN_SECOND_NAME + data.DETAIL_COUNT[i].COUNT + "人";*/
              if (i != 0) {
                self.customerType += ','
              }
              self.customerType += data.DETAIL_COUNT[i].PLAN_SECOND_NAME + data.DETAIL_COUNT[i].COUNT + "人";


            }
          } else {

//            showErrorDetailDialog(returnData.msg.message);
            MessageBox('提示', res.msg.message);
          }
        }).catch(res => {
          MessageBox('提示', res.msg.message);
          setTimeout(()=>{
            Indicator.close('搜索中...');
          },10)

        })


      },
      pullstall: function () {


        var self = this;
        if (self.location.lat == "undefined" || self.location.lat == "" || self.searchLocation.lat == "" || self.searchLocation.lng == "") {
          return;
        }
        if (self.customerNum == 0 || typeof(self.customerNum) == "undefined") {
          MessageBox('提示', "潜客数量不能为0")
          return;
        }
        if (self.location.lat != self.searchLocation.lat || self.location.lng != self.searchLocation.lng) {
          MessageBox('提示', "当前地址与摆摊地址不一致，请重新搜索潜客")
          return;
        }
        //创建客户群 接口
        var orgId = this.MCRM_orgId;
//        var radiusNum = self.getRadius().interInput;

        var baiduAddr = GDtoBD(self.location.lng, self.location.lat);
        var baiduLat = baiduAddr[1];
        baiduLat = baiduLat.toFixed(6);
//			self.lat = baiduLat;
//
        var baiduLng = baiduAddr[0];
        baiduLng = baiduLng.toFixed(6);
//			self.lng = baiduLng;
//        var params=[baiduLat,baiduLng,orgId,self.type, radiusNum];
        Indicator.open('加载中...');
        this.MCRM_ajax_createStallCustGroup(baiduLat, baiduLng, orgId, self.navSelected, self.rangNumNew).then(res => {
          Indicator.close('加载中...');
          if (res.msg.code == "1") {

//              this.localSaveGlobal.setStore('pullPositionName',this.value)
            this.$router.push({name: 'Stall',
              params: {
                pullPositionName: this.value,
                customerType: this.customerType,
                lat: this.location.lat,
                lng: this.location.lng,
                radiusNum: this.rangNumNew,
                cityCode: this.cityCode,
                navSelected: this.navSelected,
                customerNum: this.customerNum,
                pullstallCustomId: res.data.CUSTOM_ID,
                pullstallUUID: res.data.UUID
              }
            })


          } else {
            MessageBox('提示', returnData.msg.message);
          }
        }).catch(res => {
            setTimeout(()=>{
              Indicator.close('加载中...');
            },10)

          MessageBox('提示', '网络超时');
        })

        /* showPageLoading();
         DataUtils.createStallCustGroup(params,function(data){
         //数据返回，隐藏等待提示
         hidePageLoading();
         var status=data.invocationResult.MCRMReturn;
         var returnData=JSON.parse(status);
         if(returnData.msg.code=="1"){
         ZJMCCMobile.utils.setParam("radiusNum",radiusNum);
         ZJMCCMobile.utils.setParam("diameter",radiusNum*2);
         ZJMCCMobile.utils.setParam("pullstallLat",self.lat);
         ZJMCCMobile.utils.setParam("pullstallLng",self.lng);
         ZJMCCMobile.utils.setParam("pullstallLocalName",$("#searchText").val());
         ZJMCCMobile.utils.setParam("pullstallcreateComeFrom","nearbyCustomersNew.html");
         ZJMCCMobile.utils.setParam("pullstallNumtext",self.customerNum);
         ZJMCCMobile.utils.setParam("pullstallSecondType",self.type);
         ZJMCCMobile.utils.setParam("pullstallSecondTypeName",self.typename);
         ZJMCCMobile.utils.setParam("pullstallSecondTypeTxt",self.typetxt);
         ZJMCCMobile.utils.setParam("pullstallCustomId",returnData.data.CUSTOM_ID);
         ZJMCCMobile.utils.setParam("pullstallUUID",returnData.data.UUID);

         mobileChangePage("putStallNewPage.html");
         }else{
         showErrorDetailDialog(returnData.msg.message);
         }
         });*/
      },

      backBussiness(){
        this.$router.push({name: 'Stall', params: {id: '1'}});
//        window.android.colseActivity();
      }
    }

  }
</script>


<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang='less'>
  .retailStallPositionPage {
    .mint-cell {
      height: .48rem;
    }

    /*搜索框*/
    /*  .mint-search {
        height: auto;
      }
      .mint-search-list {
        display: none;
      }
      .mint-searchbar {
        padding: 0;
        height: 100%;
        background: rgba(241, 241, 241, 1);
        .mint-searchbar-cancel {
          margin-right: 10px;
        }
        .mint-searchbar-inner {
          height: 100%;
          padding: 0 .1rem;
          background: rgba(241, 241, 241, 1);
          .mint-searchbar-core {
            background: rgba(241, 241, 241, 1);
          }
        }
      }
      input::-webkit-search-cancel-button {
        display: none !important;
      }
      input[type=search]::-ms-clear {
        display: none !important;
      }*/
    /*搜索框end*/
  }


</style>
<style lang='less' scoped>

  @boderColor: #EAEAEA;
  .retailStallPositionPage {
    .positionSearch {
      margin: .17rem 0 0 .17rem;
    }
    /*搜索框*/
    /*.search {
      margin: 0 .6rem 0 .17rem;
      .searchBtn{
        position: absolute;
        top:0;
        right: .2rem;
        height: .32rem;
        a{
          display: inline-block;
          height: .32rem;
          line-height: .32rem;
        }

      }
    }
    .mint-search {
      margin-top: .14rem;
      height: .32rem;
      font-size: .13rem;
      background: rgba(241, 241, 241, 1);
      border-radius: .16rem;
    }
    .mint-searchbar-inner .mintui-search {
      font-size: .14rem;
    }*/
    /*搜索end*/
    /*搜索列表*/
    .searchList {
      position: absolute;
      top: .4rem;
      width: 100%;
      background-color: #fff;
      z-index: 10;
      max-height: 280px;
      overflow-y: scroll;
      ul li {
        padding: .08rem;
        font-size: .14rem;
        p:last-child {
          padding-top: .08rem;
          font-size: .1rem;
          color: #b8b8b8;
        }
      }
    }
    /*搜索列表end*/
    font-size: .14rem;

    .nav {
      padding: .14rem .18rem;
      overflow: hidden;
      ul {
        overflow-x: auto;
        white-space: nowrap;

        &::-webkit-scrollbar {
          display: none;
        }
        li {
          border-radius: .15rem;
          display: inline-block;
          padding: .06rem .14rem;
          font-size: .13rem;
        }
        li.selected {
          background-color: #0085CF;
          color: #fff;
        }
      }
    }

    .mescroll {
      position: fixed;
      top: 1.49rem;
      bottom: 1.97rem;
      width: 100%;
      height: auto;

      .content {
        width: 100%;
        height: 100%;
        .rangeModule {
          font-size: .14rem;
          position: absolute;
          top: .11rem;
          left: .13rem;
          z-index: 10;
          border-radius: .15rem;
          background-color: #fff;
          width: 1rem;
          text-align: center;
          li {
            border-top: 1px solid #f1f1f1;
            padding: .06rem .08rem .07rem .06rem;
            &:first-child {
              border-top: 1px solid transparent;
            }
            .iconTriDown {
              position: absolute;
              top: 50%;
              right: .05rem;
              margin-top: -.025rem;
              display: inline-block;
              width: 0;
              height: 0;
              border-width: .05rem;
              border-style: solid;
              border-color: black transparent transparent transparent;
            }
            .iconTriUP {
              position: absolute;
              top: 50%;
              right: .05rem;
              margin-top: -.07rem;
              display: inline-block;
              width: 0;
              height: 0;
              border-width: .05rem;
              border-style: solid;
              border-color: transparent transparent black transparent;
            }
          }

        }

        #container {
          width: 100%;
          height: 100%;
        }

      }
    }
    .footer {
      position: fixed;
      bottom: 0;
      height: 1.97rem;
      width: 100%;
      p {
        text-align: center;
        font-size: .16rem;
        color: #0084CE;
        padding: .59rem 0 .28rem;
      }
      .btn {
        width: 90%;
        margin: 0 auto;
      }
    }
  }




</style>
