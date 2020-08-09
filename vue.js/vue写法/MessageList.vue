<template>
  <div>
    <div v-for="(item,index) in message" :key="index">
      <div class="imgCell" @click.stop="toMessageDetail(item.name)">
        <div class="img"><img :src="item.imgUrl"><i id="redpoint" :style="{'display':item.isHave>0
          ? '':'none'}"></i>
        </div>
        <div class="text">
          <div>
            <div>
              <p>{{item.name}}</p>
              <span>{{item.description}}</span>
            </div>
          </div>
        </div>
        <div class="tip">{{item.isHave > 0 ? item.isHave : ''}}</div>
      </div>
    </div>
  </div>


</template>
<script>
    export default {
        name: 'MessageList',
        props:['message'],
        data () {
            return {
                title: '',
            }
        },
      created(){
        var params = {opId: '12'}
//        this.getMsgIsHasNewInfo(params).then((res) => {
//          if (res.msg.code == '1') {
//            console.log('结果', res);
//
//            this.messageList[0].description = res.data.teamNoticeDate;
//            this.messageList[4].description = res.data.workApprovalDate;
//            this.messageList[5].description = res.data.myStallInviteDate;
//
//
//            this.messageList[0].isHave = res.data.teamNoticeFlag;
//            this.messageList[1].isHave = res.data.taskFlag;
//            this.messageList[2].isHave = res.data.customRemindFlag;
//            this.messageList[3].isHave = res.data.shopReminder;
//            this.messageList[4].isHave = res.data.workApprovalFlag;
//            this.messageList[5].isHave = res.data.myStallInviteFlag;
//            this.messageList[6].isHave = res.data.popularSpirintFlag;
//            this.messageList[7].isHave = res.data.newColleagueFlag;
//          }
//
//
//        })
      },
      mounted(){

      },
      computed: {},
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
<style scoped>

</style>
