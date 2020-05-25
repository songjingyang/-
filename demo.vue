<template>
  <div class="detail">
    <section>
      <van-swipe class="my-swipe" :autoplay="3000" indicator-color="white">
        <van-swipe-item>1</van-swipe-item>
        <van-swipe-item>2</van-swipe-item>
        <van-swipe-item>3</van-swipe-item>
        <van-swipe-item>4</van-swipe-item>
      </van-swipe>
    </section>
    <section class="buyDetail">
      <div class="price van-clearfix">
        <a>
          <span><span class="s-word">¥</span> 439</span>
          <span class="price-tip">
            <span class="s-word">VIP后</span>
            <span class="s-word pw">¥</span>
            <span>339</span>
          </span>
        </a>
        <a class="to-vip"><span class="icon-box"><img src="../../static/img/activity-express/ask-icon.png"></span>如何成为VIP</a>
      </div>
      <div class="tips">
        <span class="icon-box"><img src="../../static/img/activity-express/horn-icon.png"></span>
        <span>青少年表达力训练营正式开始报名啦！</span>
      </div>
    </section>

    <section class="layoutImg">
      <img src="../../static/img/activity-express/bg.png">
    </section>

    <section class="btm">
      <div class="comBtn signs" @click="showPopup">立即报名</div>
    </section>

    <van-popup
      v-model="show"
      round
      :style="{ width: '80%'}"
      :class="{'popbg':isPop === 3}"
    >
      <div class="pop-box">

        <a class="closepop" @click="closePop">
          <img v-if="isPop === 3" src="../../static/img/activity-express/close-w-icon.png">
          <img v-else src="../../static/img/activity-express/close-icon.png">
        </a>

        <div v-if="isPop === 1">
          <p class="poptitle">您需要绑定手机号</p>
          <van-cell-group>

            <div class="phoneList">
            <a @click="showPick">
              +{{ areaCode }}
              <img class="downIcon" src="../../static/img/activity-express/down-icon.png">
            </a>
            <van-field
              v-model="tel"
              clearable
              size="large"
              type="tel"
              placeholder="请输入手机号" />
          </div>
          </van-cell-group>
          <van-cell-group>
            <van-field
              v-model="code"
              center
              clearable
              label="验证码"
              label-width="70"
              size="large"
              type="number"
              :maxlength="6"
              placeholder="请填写验证码"
            >
              <template #button>
                <van-button
                  class="btnSty"
                  color="#FEE433"
                  type="default"
                  size="small"
                  :loading="smsSending"
                  :disabled="smsBtnDisabled"
                  round
                  @click="getCode">{{ smsBtnText }}</van-button>
              </template>
            </van-field>
          </van-cell-group>
          <div class="nextBox">
            <van-button
              class="btnSty"
              color="#FEE433"
              type="default"
              size="large"
              :disabled="isNextAble"
              round
              @click="nextStep">下一步</van-button>
          </div>
        </div>

        <div v-if="isPop === 2">
          <p class="poptitle">提交订单</p>
          <div>
            <div class="payList">
              <span class="spec">支付总额</span>
              <span>¥{{ total }}</span>
            </div>
            <div class="payWay">支付方式</div>
            <div class="wechatList">
              <div class="wechatL">
                <img src="../../static/img/activity-express/wechat-icon.png">
                <span>微信支付</span>
              </div>
              <div class="wechatR">
                <img src="../../static/img/activity-express/confirm-icon.png">
              </div>
            </div>
          </div>
          <div class="zfBox">
            <van-button
              class="btnSty"
              color="#FEE433"
              type="default"
              size="large"
              round
              @click="confirmPayment">确认支付</van-button>
          </div>
        </div>

        <div class="scBox" v-if="isPop === 3">
          <div class="successTip">
            恭喜你报名成功！
          </div>
          <div>
            <p class="contract">
              请添加助教人员微信：157xxxxxxxx并备注您的手机号，或长按识别下方二维码
            </p>
            <div class="qrcode">
              <img src="../../static/img/activity-express/wechat-icon.png">
              <p class="saveTips">
                长按保存识别二维码
              </p>
            </div>
          </div>
        </div>

      </div>
    </van-popup>


    <van-popup v-model="pickShow" position="bottom" get-container="" @open="onOpen">
      <van-picker
        show-toolbar
        :columns="columns"
        @cancel="onCancel"
        @confirm="onConfirm"
        :loading="loading"
      />
    </van-popup>
  </div>
</template>

<script>
  import { Swipe, SwipeItem, Popup, Field, Picker } from 'vant';
  import { mapState, mapMutations } from "vuex";
  export default {
    data() {
      return {
        show: false,
        pickShow: false,
        isNextAble: true,
        columns: [],
        loading: true,
        tel: '',
        code: '',
        isPop: 1, //弹窗状态
        total:'339',
        areaCode:'86'
      };
    },
    methods: {
      ...mapMutations(["updateKV"]),
      showPopup() {
        this.show = true;
      },
      getCode() {
        this.updateKV({ smsSending: true });
        this.$net
          .POST("/misc/send-sms", { mobile: this.tel, prefix: this.code }, false)
          .then(res => {
            this.updateKV({ smsSending: false, smsTimeElapses: 91 });
            this.onSmsTimeElapse();
          })
          .catch(err => {
            console.log(err);
            this.$toast.fail({
              message: err.errmsg,
              mask: true,
              closeOnClick: true,
              icon: require("@/assets/img/hud_error.png")
            });
            this.updateKV({ smsSending: false, smsTimeElapses: 0 });
          });
      },
      onSmsTimeElapse() {
        if (this.smsTimeElapses > 0) {
          const smsTimeElapses = this.smsTimeElapses - 1;
          this.updateKV({ smsTimeElapses });
          if (smsTimeElapses > 0) {
            setTimeout(() => {
              this.onSmsTimeElapse();
            }, 1000);
          } else {
            this.updateKV({ smsSending: false });
          }
        }
      },
      closePop() {
        this.show = false;
      },
      showPick() {
        this.pickShow = true;
      },
      onConfirm(value, index) {
        let val = value.split("+")[1].replace(")", "");
        this.areaCode = val;
        this.pickShow = false;
      },
      onCancel() {
        this.pickShow = false;
      },
      onOpen() {
        if (!this.columns.length) {
          this.loading = true;
          this.$net
            .GET("/misc/countries", {}, false)
            .then(res => {
              console.log(res, "!!!");
              this.columns = res.map(item => {
                return `${item.name}(${item.prefix})`;
              });
              this.loading = false;
            })
            .catch(err => {
              console.log(err);
              this.$toast.fail({
                message: err.errmsg,
                mask: true,
                closeOnClick: true,
                icon: require("@/assets/img/hud_error.png")
              });
              this.updateKV({ smsSending: false, smsTimeElapses: 0 });
            });
        }
      },
      nextStep(){
        //点击下一步 需要调用接口判断验证码是否正确？？？是否已支付过？？？
        this.isPop = 2;
      },
      confirmPayment(){
        //点击确认支付 需要调用支付接口进行支付？？？
        this.isPop = 3;
      }
    },
    computed: {
      ...mapState(["smsSending", "smsTimeElapses", "loginCallback"]),
      smsBtnText() {
        return this.smsTimeElapses > 0
          ? `${this.smsTimeElapses}秒可重发`
          : "获取验证码";
      },
      smsBtnDisabled() {
        return (
          this.smsSending ||
          this.smsTimeElapses > 0 ||
          !this.tel ||
          this.tel.length < 6
        );
      }
    },
    watch: {
      code(val) {
        if(val && this.tel){
          this.isNextAble = false
        }
      }
    }
  };
</script>

<style lang="scss" scoped>
  .detail{
    img{
      width: 100%;
    }
    .comBtn{
      margin: 0 auto;
      background-color: #FEE433;
      height: 46px;
      line-height: 46px;
      border-radius: 8px;
      overflow: hidden;
      font-size: 18px;
      text-align: center;
    }
    .buyDetail{
      padding: 15px 20px 18px;
      span{
        display: inline-block;
        vertical-align: middle;
      }
      .price{
        font-size: 24px;
        a{
          float: left;
        }
        .to-vip{
          font-size: 12px;
          color: #FF3838;
          float: right;
          padding: 8px 0;
        }
        .price-tip{
          display: inline-block;
          font-size: 24px;
          background-color: #FFDFDF;
          border: 1px solid#FFDFDF;
          border-radius: 14px;
          color: #E61E1E;
          overflow: hidden;
          padding: 0 7px;
          .pw{
            font-weight: bold;
          }
        }
        .s-word{
          font-size: 14px;
        }
      }
      .tips{
        padding-top: 14px;
        font-size: 16px;
      }
      .icon-box{
        width: 14px;
        vertical-align: bottom;
        display: inline-block;
        margin-right: 2px;
      }
    }
    .btm{
      position: fixed;
      bottom: 0;
      left: 0;
      text-align: center;
      width: 100%;
      .signs{
        width: 334px;
      }
    }
    .pop-box{
      padding-top: 38px;
      .van-cell{
        padding-top: 18px;
        padding-bottom: 18px;
      }
      .poptitle{
        text-align: center;
        padding-bottom: 25px;
        font-size: 18px;
      }
      .next{
        width: 235px;
        margin: 38px 0 6px;
      }
      .comfirmpay{
        width: 235px;
        margin-top: 15px;
      }
      .btnSty{
        color: #333333!important;
        border-radius: 8px;
      }
      .phoneList{
        display: flex;
        align-items: center;
        a{
          width: 88px;
          padding-left: 16px;
          font-size: 16px;
        }
        .downIcon{
          width: 9px;
          display: inline-block;
        }
      }
      .payList{
        padding: 22px 20px;
        border-top: 1px solid#F5F5F5;
        font-size: 16px;
        color: #333333;
        display: flex;
        justify-content: space-between;
        .spec{
          color: #666666;
        }
      }
      .payWay{
        padding: 16px 20px;
        background-color: #F5F5F5;
        color: #999999;
        font-size: 14px;
      }
      .wechatList{
        display: flex;
        justify-content: space-between;
        padding: 16px 20px;
        font-size: 16px;
        .wechatL{
          img{
            width: 21px;
          }
        }
        .wechatR{
          img{
            width: 16px;
          }
        }
        .payC{
          width: 235px;
        }
      }
      .scBox{
        .successTip{
          font-size: 20px;
          color: #ffffff;
          text-align: center;
          font-weight: bold;
        }
        .contract{
          font-size: 14px;
          color: #666666;
          padding: 46px 20px 25px;
          line-height: 1.4;
          text-align: left;
        }
        .qrcode{
          text-align: center;
          img{
            width: 104px;
          }
          .saveTips{
            font-size: 12px;
            color: #999999;
            margin: 8px 0 36px;
          }
        }
      }
      .closepop{
        width: 22px;
        display: inline-block;
        position: absolute;
        top: 13px;
        right: 23px;
      }
      .nextBox{
        padding: 38px 42px 30px;
      }
      .zfBox{
        text-align: center;
        padding-bottom: 24px;
        background-color: #F5F5F5;
        padding: 15px 42px 23px;
      }
    }
  }
  .van-popup{
    border-radius: 8px;
  }
  .popbg{
    background: url("/img/activity-express/success-bg.png") no-repeat top;
    background-size: contain;
    background-color: #fff;
  }
  .my-swipe .van-swipe-item {
    color: #fff;
    font-size: 20px;
    line-height: 176px;
    text-align: center;
    background-color: #39a9ed;
  }
</style>