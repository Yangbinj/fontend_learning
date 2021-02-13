<template>
  <div>
    <vm-nav-bar class="mcrm-navBar"
                :title="title"
                left-text="返回"
                right-text=""
                left-arrow
                @click-left="onClickLeft"
    />
    <messageList1 :message="messageList"></messageList1>

  </div>
</template>
<script>
  import {Toast} from 'vortex-mobile';
  import messageList1 from '../../components/MessageList';
  export default {
    components: {messageList1},
    name: '',
    data () {
      return {
        title: '我的消息',
        messageList: [{
          name: '公告',
          description: '',
          messageNum: '',
          imgUrl: require('../../assets/images/mcrm/icon-message1.svg'),
          isHave: 0
        },
          {
            name: '任务消息',
            description: '',
            messageNum: '',
            imgUrl: require('../../assets/images/mcrm/icon-message2.svg'),
            isHave: 0
          },
          {
            name: '熟客商机提醒',
            description: '',
            messageNum: '',
            imgUrl: require('../../assets/images/mcrm/icon-message3.svg'),
            isHave: 0
          },
          {
            name: '用户到店提醒',
            description: '',
            messageNum: '',
            imgUrl: require('../../assets/images/mcrm/icon-message4.svg'),
            isHave: 0
          },
          {
            name: '工作审批',
            description: '',
            messageNum: '',
            imgUrl: require('../../assets/images/mcrm/icon-message5.svg'),
            isHave: 0
          },
          {
            name: '摆摊消息',
            description: '',
            messageNum: '',
            imgUrl: require('../../assets/images/mcrm/icon-message6.svg'),
            isHave: 0
          },
          {
            name: '人气精灵',
            description: '',
            messageNum: '',
            imgUrl: require('../../assets/images/mcrm/icon-message7.svg'),
            isHave: 0
          },
          {
            name: '好友申请',
            description: '',
            messageNum: '',
            imgUrl: require('../../assets/images/mcrm/icon-message8.svg'),
            isHave: 0
          }]
      }
    },
//    created(){
//      var params = {opId: '12'}
//      this.getMsgIsHasNewInfo(params).then((res) => {
//        if (res.msg.code == '1') {
//          console.log('结果', res);
//
//          this.messageList[0].description = res.data.teamNoticeDate;
//          this.messageList[4].description = res.data.workApprovalDate;
//          this.messageList[5].description = res.data.myStallInviteDate;
//
//
//          this.messageList[0].isHave = res.data.teamNoticeFlag;
//          this.messageList[1].isHave = res.data.taskFlag;
//          this.messageList[2].isHave = res.data.customRemindFlag;
//          this.messageList[3].isHave = res.data.shopReminder;
//          this.messageList[4].isHave = res.data.workApprovalFlag;
//          this.messageList[5].isHave = res.data.myStallInviteFlag;
//          this.messageList[6].isHave = res.data.popularSpirintFlag;
//          this.messageList[7].isHave = res.data.newColleagueFlag;
//        }
//
//
//      })
//    },
//    mounted(){
//
//    },
//    computed: {},
    methods: {
      toMessageDetail(index){
        Toast('跳转' + index + '页面');
      },
      onClickLeft(){
        Toast('跳转HOME页面');
      }
    },
  }
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="less" type="text/less">
  @import "../../assets/css/base";

  .imgCellAll {
    /*padding-top: 20px;*/
    & > div {
      padding-bottom: 10px;
      background-color: #fff;
    }
    & > div:first-child {
      padding-top: 20px;
    }
  }

  .imgCell {
    display: flex;
    height: 70px;
    padding: 0 10px;
    align-items: center;
    margin: 0 17px;
    box-shadow: 3px 12px 0 rgba(0, 0, 0, 0.02), 0 6px 13px 0 rgba(0, 0, 0, 0.02), 0 .1px 15px 0 rgba(0, 0, 0, 0.01), 4px 6px 20px 0 #EAEAEA;
    border-radius: 2px;
    /*justify-content: center;*/
    .img {
      margin-right: 10px;
      position: relative;
      i {
        display: block;
        width: 9px;
        height: 9px;
        border-radius: 50%;
        background: @colorRed;
        position: absolute;
        top: 0;
        right: 0;
      }
      img {
        width: 40px;
        height: 40px;
      }
    }
    .text {
      flex: 1;
      overflow: hidden;
      & > div {
        /*display: flex;*/
        width: 100%;

        p {
          height: 22px;
          font-size: 16px;
          overflow: hidden;
          text-overflow: ellipsis;
          white-space: nowrap;
          align-items: center;
          justify-content: center;
        }
        span {
          display: block;
          height: 17px;
          overflow: hidden;
          text-overflow: ellipsis;
          white-space: nowrap;
          align-items: center;
          justify-content: center;
          color: @colorGray;

        }
      }

    }
    .tip {
      color: @colorGray;
      font-size: 14px;
    }
  }

</style>
