<template>
  <div class="stallCirclePage">
    <mt-header title="工作圈">
      <mt-button slot="left" @click.prevent.stop="back"><span class="icon-goBack"></span></mt-button>
    </mt-header>
    <div class="comment" v-show="ifShowComment">
      <div>

        <a class="mint-cell mint-field">
          <div class="mint-cell-left"></div>
          <div class="mint-cell-wrapper">
            <div class="mint-cell-title"><span class="mint-cell-text">留言</span></div>
            <div class="mint-cell-value">
              <input placeholder="留言" type="text" class="mint-field-core" v-model="commentValue" autofocus v-focus>
              <div class="mint-field-clear" style="display: none;"><i class="mintui mintui-field-error"></i></div>
              <span class="mint-field-state is-default"><i class="mintui mintui-field-default"></i></span>
              <div class="mint-field-other">
                <div class="submitClass" @click="submit()">提交</div>
              </div>
            </div>
          </div>
          <div class="mint-cell-right"></div>
        </a>

      </div>

    </div>

    <section ref="mescrollAll" class="stallLlist ios_mescroll">
      <div id="mescroll" class="mescroll">

        <div class="listContent">
          <!--即将开始-->
          <div v-show="noContent" class="noContent"><p>暂无数据</p></div>
          <div class="listContentCurrent" v-for="(item,index) in stallCircleList.myWorkCircleContentBeanList">
            <div class="headTitle">
              <div class="headPhoto"></div>
              <div>
                <p>{{item.showName}}</p>
                <p class="colorGray">{{item.createDate}}</p>
              </div>

            </div>
            <div class="description">{{item.text}}</div>
            <div class="workPhoto">
              <div v-if="item.smallImage1 != ''" class="workPhotoImg"><img
                src="../../../assets/images/stall/AvatarMore.svg" alt=""></div>
              <div v-if="item.smallImage1 != ''" class="workPhotoImg"><img
                src="../../../assets/images/stall/AvatarMore.svg" alt=""></div>
              <div v-if="item.smallImage3 != ''" class="workPhotoImg"><img :src="item.smallImage3" alt=""></div>
            </div>

            <div class="contentDes colorGray">
              <div><p>签到地点: {{item.locationName}}</p></div>
              <div><p>摆摊业务: {{item.planSecondTypeName}}</p></div>
              <div class="more" @touchstart.prevent.stop="moreBtn(index)"></div>
              <div class="transBorder">
                <transition name="slide-fade">
                  <div v-show="isShowMore == index" class="moreModule">
                    <span :data-zan="ifShowZan(item.zans,MCRM_opId)"
                          @touchstart.prevent="tapZan($event,item.id,item.opId,item.showName)">点赞</span>
                    <span @touchstart.prevent="addComment(item.id)">留言</span>
                    <span @touchstart.prevent="deleteCircle(item.id)" v-show="item.opId == MCRM_opId">删除</span>
                    <i class="arrow_right"></i>
                  </div>
                </transition>
              </div>


            </div>

            <div class="like">
              <div :data-zan="ifShowZan(item.zans,MCRM_opId)"
                   :class="['likeLogo', ifShowZan(item.zans,MCRM_opId) ?'likeLogoHover': 'likeLogoNoZan']"
                   @click.prevent="cancelZan($event)">

              </div>
              <span class="likeCount">{{item.zans.length}}</span>
              <span class="likeName" v-for="name in item.zans">{{name.showName}} </span>
            </div>


            <div class="leaveMessage" v-if="item.replays.length">
              <div class="leaveMessageCurrent" v-for="message in item.replays"><span
                class="likeName">{{message.reply.showName}}:</span>{{message.reply.commentContent}}
              </div>
            </div>


          </div>

        </div>


      </div>
    </section>


  </div>
</template>

<script>

  import MeScroll from 'mescroll.js'
  import {mapGetters, mapMutations} from 'vuex'
  import {Indicator} from 'mint-ui'
  import {MessageBox} from 'mint-ui'


  export default {
//    name: 'RetailOrderDetails',
    data () {
      return {
        stallCircleList: {},
        isShowMore: -1,
        commentValue: '',
        ifShowComment: false,
        commentId: null,
        noContent:false
      }
    },

    computed: {
      ...mapGetters([
        'MCRM_opId',
        'MCRM_stallId',
        'MCRM_loginName'
      ]),
      ifShowZan () {
        return function (json, opId) {
          let zanId = false
          for (var i = 0; i < json.length; i++) {
            if (json[i].opId == opId) {
              zanId = json[i].id
              return zanId
            }
          }

          return zanId
        }
      },

    },
    /*   directives: {
     /!*      focus: {
     inserted: function (el, {value}) {
     if (value) {
     el.focus();
     }
     }
     },*!/

     focus: {
     // 指令的定义
     inserted: function (el) {
     el.focus()
     }
     }

     },*/
    created(){
      Indicator.close('加载中...')
      console.log('created-----');
      this.loginOpId = this.MCRM_opId;
//      self.loginOpId = self.localSaveGlobal.getStore("loginOpId");
    },
    mounted(){
      this.originFontSize = window.getComputedStyle(document.documentElement).fontSize;
      document.documentElement.style.fontSize = document.documentElement.clientWidth / 414 * 100 + 'px';
//      this.orderDetailsInit()
      this.mescrollInit()

      console.log("ok2", window.location.href);

      this.$refs.mescrollAll.addEventListener("touchstart", () => {
        this.cancelPrevent()
      })

    },
//    过滤方法
    filters: {},
    destroyed(){
      document.documentElement.style.fontSize = parseInt(this.originFontSize) + 'px';
    },
    methods: {
//        取消
      back(){
        this.$router.push({name: 'StallDetail'});
      },
      cancelPrevent(){
        this.isShowMore = -1
        this.ifShowComment = false
      },
//      评论提交
      submit(){
        this.ifShowComment = false
        console.log(this.commentValue, this.MCRM_opId, this.MCRM_loginName, '', this.commentId);
        this.MCRM_ajax_addComments(this.commentValue, this.MCRM_opId, this.MCRM_loginName, '', this.commentId).then(res => {
          Indicator.close('加载中...')
          if (res.msg.code == '1') {
            this.mescroll.resetUpScroll(true);
          } else {
            this.mescroll.endErr()
            MessageBox('提示', res.msg.message)
          }
        }).catch(res => {
            setTimeout(()=>{
              Indicator.close('加载中...')
            },10)

          MessageBox('提示', '网络超时')
        })

      },

//      滑动组件mescroll
      mescrollInit(){
        var self = this;
        self.mescroll = new MeScroll("mescroll", {
          //下拉刷新的所有配置项
          down: {
            callback: function (mescroll) {
              //下拉刷新的回调,默认重置上拉加载列表为第一页(down的auto默认true,初始化Mescroll之后会自动执行到这里,而mescroll.resetUpScroll会触发up的callback)
              mescroll.resetUpScroll();

            },
            inOffset: function (mescroll) {
              self.cancelPrevent()
            },
          },
          //上拉加载的所有配置项
          up: {
            use: true, //是否初始化上拉加载; 默认true
            auto: true, //是否在初始化时以上拉加载的方式自动加载第一页数据; 默认true
            isLock: false, //是否锁定上拉,默认false
            isBoth: true, //上拉加载时,如果滑动到列表顶部是否可以同时触发下拉刷新;默认false,两者不可同时触发; 这里为了演示改为true,不必等列表加载完毕才可下拉;
            isBounce: false, //是否允许ios的bounce回弹;默认true,允许回弹; 此处配置为false,可解决微信,QQ,Safari等等iOS浏览器列表顶部下拉和底部上拉露出浏览器灰色背景和卡顿2秒的问题
            callback: self.getListData, //上拉回调,此处可简写; 相当于 callback: function (page, mescroll) { getListData(page); }
            page: {
              num: 0, //当前页 默认0,回调之前会加1; 即callback(page)会从1开始
              size: 20, //每页数据条数
              time: null //加载第一页数据服务器返回的时间; 防止用户翻页时,后台新增了数据从而导致下一页数据重复;
            },
            htmlNodata: '<p class="upwarp-nodata">-- 没有更多数据了 --</p>',
            onScroll: function (mescroll, y, isUp) { //列表滑动监听,默认onScroll: null;
              self.cancelPrevent()
            },

          }
        });
      },

      getListData(page) {
        //联网加载数据
        let self = this
        self.getListDataFromNet(page.num, page.size, function (curPageData, total) {
          if (page.num == 1) {

            self.stallCircleList = {}
            self.isShowMore = -1
            self.commentValue = ''
            self.ifShowComment = false
            self.commentId = null

            self.stallCircleList = {}
            self.stallCircleList = curPageData
            if (self.stallCircleList.myWorkCircleContentBeanList.length == 0) {
              self.noContent = true
            }


          }

          /*  if (page.num > 1) {
           self.stallCircleList = curPageData
           }*/


//          for (var i = 0; i < curPageData.length; i++) {
//            self.stallLists.push(curPageData[i]);
//          }

          self.mescroll.endBySize(curPageData.myWorkCircleContentBeanList.length, total); //必传参数(当前页的数据个数, 总数据量)
        }, function () {
          //联网失败的回调,隐藏下拉刷新和上拉加载的状态;
          self.mescroll.endErr();
        })

      },

      getListDataFromNet(pageNum, pageSize, successCallback, errorCallback) {
//          let params = ['1','20','10196174']
//        console.log('this :',params);

        this.MCRM_ajax_getMyWorkCircleContentListForStall(this.MCRM_stallId).then((res) => {
          console.log('结果-----------------:', res);
          var lists = res.data;

          var total = parseInt(lists.myWorkCircleContentBeanList.length);

          successCallback(lists, total);
        });
      },

//      点赞,评论,删除 功能弹出
      moreBtn(index){
        this.ifShowComment = false
        if (index == this.isShowMore) {
          this.isShowMore = -1
        } else {
          this.isShowMore = index
        }

      },
//      点赞
      tapZan(event, id, opId, showName){
        let showZan = event.currentTarget.getAttribute('data-zan')

        if (!showZan) {
          Indicator.open('加载中...')
          this.MCRM_ajax_addZan(opId, showName, id).then(res => {
            Indicator.close('加载中...')
            if (res.msg.code == 1) {
              this.mescroll.resetUpScroll(true);

            } else {
              MessageBox('提示', res.msg.message)
            }

          }).catch(res => {
              setTimeout(()=>{
                Indicator.close('加载中...')
              },10)

            MessageBox('提示', '网络超时')
          })
        } else {
          MessageBox('提示', '你已经点过赞了')
        }


      },
//      取消赞
      cancelZan(event){

        let showZan = event.currentTarget.getAttribute('data-zan')

        if (showZan) {
          Indicator.open('加载中...')
          this.MCRM_ajax_cancelZan(showZan).then(res => {
            console.log(res);
            Indicator.close('加载中...')
            if (res.msg.code == 1) {
              this.mescroll.resetUpScroll(true);

              console.log('res取消在赞', res)
            } else {
              MessageBox('提示', res.msg.message)
            }

          }).catch(res => {
              setTimeout(()=>{
                Indicator.close('加载中...')
              },10)

            MessageBox('提示', '网络超时')

          })

        } else {
          return
        }

      },
//      点击留言弹出
      addComment(id){
        this.commentId = id
        console.log('你好')
        this.commentValue = ''

        setTimeout(() => {
          this.ifShowComment = true

        }, 20)
      },
//      删除发布的工作圈
      deleteCircle(id){
        Indicator.open('加载中...')
        this.MCRM_ajax_deletecircle(id).then(res => {
          Indicator.close('加载中...')
          console.log('删除:', res);
          if (res.msg.code == '1') {
            this.mescroll.resetUpScroll(true);

          } else {
            MessageBox('提示', res.msg.message)
          }
        }).catch(res => {
            setTimeout(()=>{
              Indicator.close('加载中...')
            },10)
          MessageBox('提示', '网络超时')
        })
      }

    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang='less'>


  .stallCirclePage {
    .mint-cell:last-child {
      background-image: none;
    }
    .mint-cell-title {
      display: none;
    }

  }

</style>
<style lang="less" scoped>

  .stallCirclePage {

    .comment {
      font-size: .16rem !important;
      border: 1px solid #dedede;
      position: fixed;
      width: 100%;
      bottom: 0;
      left: 0;
      z-index: 10001;
      padding: .2rem;
      box-sizing: border-box;
      background: #fff;
      & > div {
        border: 1px solid #dedede;
      }
      .submitClass {
        color: #fff;
        padding: .1rem .2rem;
        background-color: #66bec3;
        border-radius: 5px;
      }
    }

    /* 可以设置不同的进入和离开动画 */
    /* 设置持续时间和动画函数 */
    .slide-fade-enter-active {
      transition: all .3s ease;
    }
    .slide-fade-leave-active {
      transition: all .3s ease;
    }
    .slide-fade-enter, .slide-fade-leave-to
      /* .slide-fade-leave-active for below version 2.1.8 */
    {
      transform: translateX(100%);
      opacity: 0;
    }

    font-size: .14rem;
    .mint-button:after {
      border-radius: .4rem;
    }

    .stallLlist {
      font-size: .14rem;
      position: fixed;
      top: .51rem;
      bottom: 0;
      width: 100%;
      height: auto;
      .colorGray {
        color: #848C97;
      }
      .listContent {
        padding: .17rem;
        .noContent{
          p{
            font-size: .16rem;
            text-align: center;
          }
        }
        .listContentCurrent {
          margin-bottom: .1rem;
          position: relative;
          .likeName {
            color: #3c5b97;
          }
          .headTitle {
            overflow: hidden;
            & > div {
              float: left;
              p {
                padding: .06rem 0;
              }
            }
            .headPhoto {
              width: .46rem;
              height: .46rem;
              background: url("../../../assets/images/stall/Avatar.svg");
              background-size: 100% 100%;
              margin-right: .16rem;
            }
          }
          .description {
            margin-top: .2rem;
            word-break: break-all;
            line-height: .2rem;
          }
          .workPhoto {
            margin: .18rem 0 .1rem;
            overflow: hidden;
            .workPhotoImg {
              float: left;
              margin-right: .19rem;
              img {
                width: .6rem;
                height: .6rem;
              }
            }
          }
          .contentDes {
            position: relative;
            padding-right: .4rem;
            p {
              line-height: .2rem;
              word-break: break-all;
            }
            .more {
              position: absolute;
              bottom: 0;
              right: 0;
              width: .2rem;
              height: .2rem;
              background: url("../../../assets/images/stall/more.svg");
              background-size: 100% 100%;
            }
            .transBorder {
              overflow: hidden;
              position: absolute;
              bottom: .3rem;
              right: .2rem;
              height: .2rem;
            }
            .moreModule {
              span {
                color: #ffffff;
                line-height: .2rem;
                display: inline-block;
                background-color: #3c5b97;
                width: .45rem;
                text-align: center;
                border-right: 1px solid #4e72b8;
                float: left;
              }
              .arrow_right {
                display: inline-block;
                width: 0;
                height: 0;
                border-width: .06rem;
                border-style: solid;
                border-color: transparent transparent transparent #3c5b97;
                vertical-align: middle;
                position: relative;
                top: .015rem;
              }
            }
          }
          .like {
            padding-top: .1rem;
            overflow: hidden;
            padding-bottom: .1rem;
            & > div {
              display: inline-block;
              word-break: break-all;
              line-height: .2rem;

            }
            span {
              line-height: .2rem;
            }
            .likeLogo {
              width: .2rem;
              height: .2rem;
              vertical-align: middle;
              cursor: pointer;
            }
            .likeLogoNoZan {
              background: url("../../../assets/images/stall/like.svg");
              background-size: 100% 100%;
            }
            .likeLogoHover {
              background: url("../../../assets/images/stall/likeHover.svg");
              background-size: 100% 100%;
            }
            .likeCount {
              margin: 0 .05rem;
            }

          }
          .leaveMessage {
            padding: .1rem 0;
            border-bottom: 1px solid #848C97;
            border-top: 1px solid #848C97;
            .leaveMessageCurrent {
              padding: .05rem 0;
            }
          }

        }
      }
    }

  }


</style>
