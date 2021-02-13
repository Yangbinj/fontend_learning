<template>
  <div class="remindPartnerPage">
    <!--<mt-popup
      v-model="popupVisible"
      popup-transition="popup-fade">
      <div class="teamDetail">
        <div class="closeTeamDetail" @click.prevent.stop="popupVisible = false"></div>
        <div class="teamDetailSection">
          <div class="teamDetailTitle"><p>{{teamDetailTitle}}</p></div>
          <div class="teamDetailContent">
            <div class="mint-checklist">
              <label class="mint-checklist-title"></label>
              <a class="mint-cell" v-for="item in teamDetailOptions">&lt;!&ndash;&ndash;&gt;
                <div class="cellImage"></div>
                <div class="mint-cell-left"></div>
                <div class="mint-cell-wrapper">
                  <div class="mint-cell-title">&lt;!&ndash;&ndash;&gt;
                    <label class="mint-checklist-label">
                    <span class="mint-checkbox is-right">
                       <span>{{item.status}}</span>
                      <input type="checkbox" class="mint-checkbox-input"
                             :value="item.value"
                             :disabled="item.disabled"
                             v-model="teamDetailCheckList">
                      <span class="mint-checkbox-core"></span>
                    </span>
                      <span class="mint-checkbox-label">{{item.label}}</span>
                    </label>
                  </div>
                  <div class="mint-cell-value"><span></span></div> &lt;!&ndash;&ndash;&gt;</div>
                <div class="mint-cell-right"></div>
              </a>
            </div>

          </div>
        </div>

      </div>
    </mt-popup>-->

    <mt-header :title="title">
      <mt-button slot="left" @click.prevent.stop="backBussiness"><span class="icon-goBack"></span></mt-button>
      <mt-button slot="right" @click.prevent.stop="partnerSubmit"><span class="icon-headerSure"></span></mt-button>
    </mt-header>

    <section>
      <div class="remindPartnerContent">
        <div class="searchPart">
          <div class="search">
            <div class="mint-search">
              <div class="mint-searchbar">
                <div class="mint-searchbar-inner">
                  <i class="mintui mintui-search"></i>
                  <input ref="input" v-model="searchValue" type="search" placeholder="请输入要搜索的用户…"
                         class="mint-searchbar-core"
                         @focus="searchOn">
                </div>
                <a class="mint-searchbar-cancel" v-show="inputVisible" @click.prevent.stop="searchCancel">取消</a>

              </div>

            </div>
            <div class="searchBtn">
              <a class="mint-searchbar-cancel " v-show="true" @click.prevent.stop="searechPotentialCustomer">搜索</a>
            </div>


          </div>
        </div>
        <div class="contentTitle">
          选择好友
        </div>
        <div class="partnerList mescroll" id="mescroll">
          <div>
            <!--重新封装checkList-->
            <!--团队列表-->

           <!-- <div class="mint-checklist">
              <label class="mint-checklist-title">团队列表</label>
              <a class="mint-cell" v-for="item in teamOptions">&lt;!&ndash;&ndash;&gt;
                <div class="cellImageMore"></div>
                <div class="mint-cell-left"></div>
                <div class="mint-cell-wrapper">
                  <div class="mint-cell-title">&lt;!&ndash;&ndash;&gt;
                    <label class="mint-checklist-label teamClick">
                      <span class="mint-checkbox is-right">
                      <input type="checkbox" class="mint-checkbox-input" :value="item.value"
                             :disabled="item.disabled"
                             v-model="teamCheckList">
                    <span class="mint-checkbox-core"></span>
                      </span>
                    </label>
                    <div class="mint-teamName" @click.prevent.stop="showTeamDetail(item.value,item.label)">

                      <span class="mint-checkbox-label">{{item.label}}({{item.teamUserCount}})</span>
                    </div>

                  </div>
                  <div class="mint-cell-value"><span></span></div> &lt;!&ndash;&ndash;&gt;</div>
                <div class="mint-cell-right"></div>
              </a>
            </div>-->

            <!--个人列表-->
            <div class="mint-checklist">
              <label class="mint-checklist-title"></label>
              <a class="mint-cell" v-for="item in userOptions"><!---->
                <div class="cellImage"></div>
                <div class="mint-cell-left"></div>
                <div class="mint-cell-wrapper">
                  <div class="mint-cell-title"><!---->
                    <label class="mint-checklist-label">
                    <span class="mint-checkbox is-right">
                       <span>{{item.status}}</span>
                      <input type="checkbox" class="mint-checkbox-input"
                             :value="{'opId':item.value,'showName':item.label}"
                             :disabled="item.disabled"
                             v-model="userCheckList">
                      <span class="mint-checkbox-core"></span>
                    </span>
                      <span class="mint-checkbox-label">{{item.label}}</span>
                    </label>
                  </div>
                  <div class="mint-cell-value"><span></span></div> <!----></div>
                <div class="mint-cell-right"></div>
              </a>
            </div>
            <!--重新封装checkList-->

            <!--重新封装checkList END-->
          </div>


        </div>
      </div>


    </section>

  </div>
</template>

<script>
  import {mapGetters} from 'vuex'
  import {Indicator} from 'mint-ui'
  import {MessageBox} from 'mint-ui'
  import MeScroll from 'mescroll.js'
  import {Popup} from 'mint-ui';

  export default {
//    name: 'RetailOrderDetails',
    data () {
      return {
        title: '邀请伙伴',
        searchValue: '',
        inputVisible: false,
//        teamCheckList: [],
        userCheckList: [],
//        teamOptions: [],
        userOptions: [],
      /*  teamDetailOptions: [],
        teamDetailCheckList: [],
        popupVisible: false,
        teamDetailTitle: '',*/


      }

    },

    computed: {
      ...mapGetters([
        'MCRM_opId',
        'MCRM_stallId'
      ])
    },
    created(){
//      Indicator.open("加载中...")
//      this.stallDetailId = '4119366'
//      this.opId = this.localSaveGlobal.getStore('loginOpId')
      this.opId = this.MCRM_opId
//      this.stallId = this.localSaveGlobal.getStore('stallId')
      this.stallId = this.MCRM_stallId


      if(this.$route.params.remindPeopleArr){
        this.userCheckList = this.userCheckList.concat(this.$route.params.remindPeopleArr)
      }

    },
    mounted(){

      this.originFontSize = window.getComputedStyle(document.documentElement).fontSize;
      document.documentElement.style.fontSize = document.documentElement.clientWidth / 414 * 100 + 'px';
//      this.orderDetailsInit()
      this.getPartnerList()
      console.log("ok2", window.location.href);


    },
//    过滤方法
    filters: {},
    destroyed(){
      document.documentElement.style.fontSize = parseInt(this.originFontSize) + 'px';
    },
    methods: {
      //      数据加载
      getPartnerList(){
        //      初始化mescroll
        let self = this
        self.mescroll = new MeScroll("mescroll", {
          //下拉刷新的所有配置项
          down: {
            callback: function (mescroll) {
              //下拉刷新的回调,默认重置上拉加载列表为第一页(down的auto默认true,初始化Mescroll之后会自动执行到这里,而mescroll.resetUpScroll会触发up的callback)
              mescroll.resetUpScroll();
            }
          },
          //上拉加载的所有配置项
          up: {
            use: true, //是否初始化上拉加载; 默认true
            auto: true, //是否在初始化时以上拉加载的方式自动加载第一页数据; 默认true
//          isLock: false, //是否锁定上拉,默认false
//          isBoth: true, //上拉加载时,如果滑动到列表顶部是否可以同时触发下拉刷新;默认false,两者不可同时触发; 这里为了演示改为true,不必等列表加载完毕才可下拉;
            isBounce: false, //是否允许ios的bounce回弹;默认true,允许回弹; 此处配置为false,可解决微信,QQ,Safari等等iOS浏览器列表顶部下拉和底部上拉露出浏览器灰色背景和卡顿2秒的问题
            callback: self.getListData, //上拉回调,此处可简写; 相当于 callback: function (page, mescroll) { getListData(page); }
            page: {
              num: 0, //当前页 默认0,回调之前会加1; 即callback(page)会从1开始
              size: 50, //每页数据条数
              time: null //加载第一页数据服务器返回的时间; 防止用户翻页时,后台新增了数据从而导致下一页数据重复;
            },
            htmlNodata: '<p class="upwarp-nodata">-- 没有更多数据了 --</p>', //无数据的布局
            /* onScroll: function (mescroll, y) { //列表滑动监听,默认onScroll: null;
             //y为列表当前滚动条的位置
             //            console.log("up --> onScroll 列表当前滚动的距离 y = " + y);
             self.localSaveGlobal.setStore('orderManagerScroll', y)
             }*/

          }

        })
      },
      getListData(page) {
        //联网加载数据
        var self = this
        self.getListDataFromNet(page.num, page.size, function (curPageData, total) {
          console.log('结果:',curPageData);
          if (page.num == 1) {
//            self.teamOptions = []
            self.userOptions = []
//            self.teamCheckList = []
//            self.userCheckList = []

            curPageData.userInfoList.forEach(item => {
              let status = ''
              let statusDis = false

              if (item.inviteStatus == 1) {
                status = "待确认";
                self.userCheckList.push(item.opId)
              } else if (item.inviteStatus == 2) {
                status = "已加入";
                statusDis = true;
                self.userCheckList.push(item.opId)
              } else if (item.inviteStatus == 3) {
                status = "已拒绝";

              }
              let userTep = {
                label: item.realName,
                value: item.opId,
                disabled: statusDis,
                status: status
              }
              self.userOptions.push(userTep)
            })
          /*  curPageData.teamInfoList.forEach(item => {

              let teamTep = {
                label: item.teamName,
                value: item.teamCode,
                disabled: false,
                teamUserCount: item.teamUserCount
              }
              self.teamOptions.push(teamTep)
            })*/

          }
          if (page.num > 1) {
            curPageData.userInfoList.forEach(item => {
              let status = ''
              let statusDis = false

              if (item.inviteStatus == 1) {
                status = "待确认";
                self.userCheckList.push(item.opId)
              } else if (item.inviteStatus == 2) {
                status = "已加入";
                statusDis = true;
                self.userCheckList.push(item.opId)
              } else if (item.inviteStatus == 3) {
                status = "已拒绝";
              }
              let userTep = {
                label: item.realName,
                value: item.opId,
                disabled: statusDis,
                status: status
              }
              self.userOptions.push(userTep)
            })

          }

          self.mescroll.endBySize(curPageData.userInfoList.length, total); //必传参数(当前页的数据个数, 总数据量)
        }, function () {
          //联网失败的回调,隐藏下拉刷新和上拉加载的状态;
          self.mescroll.endErr();
        })
      },
      getListDataFromNet(pageNum, pageSize, successCallback, errorCallback) {
//          var params = ["12", orderSta, pageNum, pageSize];
        Indicator.open('加载中...');

        this.MCRM_ajax_getMyTeamList(pageSize, pageNum, "20027516",this.searchValue).then((res) => {
          Indicator.close('加载中...')
          if (res.msg.code == 1) {
            var lists = res.data
            var total = parseInt(res.data.userTotalCount);
            successCallback(lists, total);
          } else {
              console.log(pageSize, pageNum, "20027516", '')
            MessageBox('提示', res.msg.message)
          }
        }).catch(res => {
          Indicator.close('加载中...')
          MessageBox('提示', '网络超时')
        });
      },


//        跳转
      backBussiness(){
        this.$router.push({name: 'StallAddImg'});
      },
      searechPotentialCustomer(){
        this.mescroll.resetUpScroll();
      },
      //        搜索框事件
      searchCancel(){
        this.inputVisible = false
        this.searchValue = ''
        this.$refs.input.blur()
        this.mescroll.resetUpScroll();
      },
//      搜索框点击事件
      searchOn(){
        this.inputVisible = true
      },


//      团队列表展示

//      提交事件
      partnerSubmit(){
        this.$router.push({name:'StallAddImg',params:{"selectedPeople":this.userCheckList}})
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang='less'>
  .remindPartnerPage {

    /*团队信息弹窗*/
    .mint-popup {
      background-color: transparent;
    }

    /*checkList*/
    .mint-checklist .mint-cell {
      padding: .1rem 0 .1rem .36rem;
      min-height: .24rem;
      border-bottom: 1px solid #EAEAEA;
    }
    .mint-cell:last-child {
      background-image: none
    }
    .mint-checklist-title {
      font-size: .16rem;
      margin: .1rem 0 0 0;
    }
    .mint-cell-wrapper {
      font-size: .16rem;
      background-image: none;
      padding: 0;
    }
    .mint-checklist-label {
      padding: 0;
    }
    .mint-checkbox-core {
      width: .2rem;
      height: .2rem;
    }
    .mint-checkbox-core::after {
      border: 2px solid transparent;
      border-left: 0;
      border-top: 0;
      content: " ";
      top: .03rem;
      left: .06rem;
      position: absolute;
      width: .04rem;
      height: .08rem;
    }
    .teamClick {
      /*padding-right: .24rem;*/
      /*float: right;*/
      position: absolute;
      top: .1rem;
      right: 0;
    }
    .mint-teamName {
      margin-right: .24rem;
    }
    .mint-checkbox-label {
      word-break: break-all
    }

  }

</style>
<style lang="less" scoped>

  @borderColor: #e1e1e1;

  .remindPartnerPage {
    font-size: .14rem;
    .cellImageMore {
      position: absolute;
      top: 50%;
      left: .12rem;
      margin-top: -0.12rem;
      width: .24rem;
      height: .24rem;
      background: url("../../../assets/images/stall/AvatarMore.svg");
      background-size: 100% 100%;
    }
    .cellImage {
      position: absolute;
      top: 50%;
      left: .12rem;
      margin-top: -0.12rem;
      width: .24rem;
      height: .24rem;
      background: url("../../../assets/images/stall/Avatar.svg");
      background-size: 100% 100%;
    }
    /*团队信息详情*/

    .remindPartnerContent {
      padding: .17rem .17rem;
      .contentTitle {
        font-size: .16rem;
        padding: .21rem 0;
      }
    }
    .partnerList {
      padding: 0 .17rem;
      position: fixed;
      top: 1.5rem;
      left: 0;
      bottom: 0;
      width: 100%;
      height: auto;
      overflow-y: auto;
      box-sizing: border-box;

    }

  }


</style>
