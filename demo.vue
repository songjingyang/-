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
      <div class="price">
        <p>
          <span>¥</span>439
        </p>
        <p>
          <span>VIP后</span>
          <span>¥</span>
          <span>339</span>
        </p>
        <p @click="toNewPage">
          <span>
            <img src="/img/activity-express/ask-icon.png" />
          </span>
          <span>如何成为VIP</span>
        </p>
      </div>
      <div class="tips">
        <span class="icon-box">
          <img src="/img/activity-express/horn-icon.png" />
        </span>
        <span>青少年表达力训练营正式开始报名啦！</span>
      </div>
    </section>

    <section class="layoutImg">
      <img src="../../static/img/activity-express/bg.png" />
    </section>

    <section class="btm">
      <div class="comBtn signs" @click="showPopup">立即报名</div>
    </section>
    <section class="place"></section>
    <van-popup v-model="show" round :style="{ width: '80%'}" :class="{'popbg':isPop === 3}">
      <div class="pop-box">
        <a class="closepop" @click="closePop">
          <img v-if="isPop === 3" src="../../static/img/activity-express/close-w-icon.png" />
          <img v-else src="../../static/img/activity-express/close-icon.png" />
        </a>

        <div v-if="isPop === 1">
          <p class="poptitle">您需要绑定手机号</p>
          <van-cell-group>
            <div class="phoneList">
              <a>
                {{ areaCode }}
                <img
                  class="downIcon"
                  src="../../static/img/activity-express/down-icon.png"
                />
              </a>
              <van-field v-model="tel" clearable size="large" type="tel" placeholder="请输入手机号" />
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
                  @click="getCode"
                >获取验证码</van-button>
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
              @click="nextStep"
            >下一步</van-button>
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
                <img src="../../static/img/activity-express/wechat-icon.png" />
                <span>微信支付</span>
              </div>
              <div class="wechatR">
                <img src="../../static/img/activity-express/confirm-icon.png" />
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
              @click="confirmPayment"
            >确认支付</van-button>
          </div>
        </div>

        <div class="scBox" v-if="isPop === 3">
          <div class="successTip">恭喜你报名成功！</div>
          <div>
            <p class="contract">请添加助教人员微信：157xxxxxxxx并备注您的手机号，或长按识别下方二维码</p>
            <div class="qrcode">
              <!-- <img src="../../static/img/activity-express/wechat-icon.png" /> -->
              <qrcode-vue :value="qrcode" level="H"></qrcode-vue>
              <p class="saveTips">长按保存识别二维码</p>
            </div>
          </div>
        </div>
      </div>
    </van-popup>

    <van-popup v-model="pickShow" position="bottom" get-container @open="onOpen">
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
import { Swipe, SwipeItem, Popup, Field } from "vant";
import QrcodeVue from "qrcode.vue";
export default {
  data() {
    return {
      show: false,
      isCodeAble: true,
      tel: "",
      digit: "",
      isPop: 3, //弹窗状态
      total: "339",
      areaCode: "+86",
      qrcode: "https://example.com"
    };
  },
  components: {
    QrcodeVue
  },
  methods: {
    showPopup() {
      this.show = true;
    },
    getCode() {},
    closePop() {
      this.show = false;
    },
    toNewPage() {
      window.open("https://vip.koipc.com/");
    }
  },
  watch: {
    tel(val) {
      if (val) {
        this.isCodeAble = false;
      } else {
        this.isCodeAble = true;
      }
    }
  }
};
</script>

<style lang="scss" scoped>
.detail {
  height: 4413px;
  img {
    width: 100%;
  }
  .comBtn {
    margin: 0 auto;
    background-color: #fee433;
    height: 46px;
    line-height: 46px;
    border-radius: 8px;
    overflow: hidden;
    font-size: 18px;
    text-align: center;
  }
  .buyDetail {
    height: 95px;
    width: 100%;
    display: flex;
    flex-direction: column;
    .price {
      height: 33px;
      margin-top: 10px;
      margin-bottom: 12px;
      width: 100%;
      display: flex;
      flex-direction: row;
      justify-content: inherit;
      align-items: center;
      p:nth-child(1) {
        margin-top: 3.5px;
        margin-bottom: 3.5px;
        margin-left: 20px;
        width: 59px;
        height: 24px;
        display: flex;
        flex-direction: row;
        justify-content: inherit;
        align-items: center;
        font-family: PingFangSC-Regular, PingFang SC;
        font-weight: 400;
        font-size: 24px;
        color: rgba(0, 0, 0, 1);
        line-height: 20px;
        span {
          font-size: 14px;
          display: inline-block;
          height: 14px;
          width: 14px;
        }
      }
      p:nth-child(2) {
        width: 103px;
        height: 28px;
        background: rgba(255, 223, 223, 1);
        border-radius: 14px;
        display: flex;
        flex-direction: row;
        margin-left: 7px;
        margin-right: 95px;
        justify-content: inherit;
        align-items: center;
        span:nth-child(1) {
          font-size: 12px;
          font-family: PingFangSC-Regular, PingFang SC;
          font-weight: 400;
          color: rgba(230, 30, 30, 1);
          margin-left: 11px;
          letter-spacing: 0.28px;
        }
        span:nth-child(2) {
          display: inline-block;
          height: 24px;
          font-size: 16px;
          font-family: LiGothicMed;
          color: rgba(230, 30, 30, 1);
          line-height: 24px;
        }
        span:nth-child(3) {
          display: inline-block;
          height: 24px;
          font-size: 24px;
          font-family: LiGothicMed;
          color: rgba(230, 30, 30, 1);
          line-height: 24px;
        }
      }
      p:nth-child(3) {
        min-width: 71px;
        height: 14px;
        display: flex;
        flex-direction: row;
        justify-content: inherit;
        align-items: center;
        span:nth-child(1) {
          display: inline-block;
          height: 10px;
          width: 10px;
          margin-right: 4px;
          img {
            width: 100%;
          }
        }
        span:nth-child(2) {
          min-width: 57px;
          height: 14px;
          font-size: 10px;
          font-family: PingFangSC-Regular, PingFang SC;
          font-weight: 400;
          color: rgba(255, 56, 56, 1);
          line-height: 14px;
        }
      }
    }
    .tips {
      height: 22px;
      width: 100%;
      display: flex;
      flex-direction: row;
      justify-content: inherit;
      align-items: center;
      .icon-box {
        display: inline-block;
        height: 15px;
        width: 15px;
        margin-right: 7px;
        margin-left: 20px;
        img {
          width: 100%;
        }
      }
      span {
        font-size: 16px;
        font-family: PingFangSC-Regular, PingFang SC;
        font-weight: 400;
        color: rgba(0, 0, 0, 1);
      }
    }
  }
  .btm {
    position: fixed;
    bottom: 0;
    left: 0;
    text-align: center;
    width: 100%;
    .signs {
      width: 334px;
    }
  }
  .pop-box {
    padding-top: 38px;
    .van-cell {
      padding-top: 18px;
      padding-bottom: 18px;
    }
    .poptitle {
      text-align: center;
      padding-bottom: 25px;
      font-size: 18px;
    }
    .next {
      width: 235px;
      margin: 38px 0 6px;
    }
    .comfirmpay {
      width: 235px;
      margin-top: 15px;
    }
    .btnCode {
      color: #333333 !important;
      border-radius: 8px;
    }
    .phoneList {
      display: flex;
      align-items: center;
      a {
        width: 88px;
        padding-left: 16px;
        font-size: 16px;
      }
      .downIcon {
        width: 9px;
        display: inline-block;
      }
    }
    .payList {
      padding: 22px 20px;
      border-top: 1px solid#F5F5F5;
      font-size: 16px;
      color: #333333;
      display: flex;
      justify-content: space-between;
      .spec {
        color: #666666;
      }
    }
    .payWay {
      padding: 16px 20px;
      background-color: #f5f5f5;
      color: #999999;
      font-size: 14px;
    }
    .wechatList {
      display: flex;
      justify-content: space-between;
      padding: 16px 20px;
      font-size: 16px;
      .wechatL {
        img {
          width: 21px;
        }
      }
      .wechatR {
        img {
          width: 16px;
        }
      }
      .payC {
        width: 235px;
      }
    }
    .scBox {
      .successTip {
        font-size: 20px;
        color: #ffffff;
        text-align: center;
        font-weight: bold;
      }
      .contract {
        font-size: 14px;
        color: #666666;
        padding: 46px 20px 25px;
        line-height: 1.4;
        text-align: left;
      }
      .qrcode {
        text-align: center;
        img {
          width: 104px;
        }
        .saveTips {
          font-size: 12px;
          color: #999999;
          margin: 8px 0 36px;
        }
      }
    }
    .closepop {
      width: 22px;
      display: inline-block;
      position: absolute;
      top: 13px;
      right: 23px;
    }
    .btBox {
      text-align: center;
      padding-bottom: 24px;
    }
    .greyb {
      background-color: #f5f5f5;
    }
  }
}
.van-popup {
  border-radius: 8px;
}
.popbg {
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
.place {
  width: 100%;
  height: 46px;
}
</style>
