<template>
  <div class="xtx-pay-page">
    <div class="container">
      <XtxBread>
        <XtxBreadItem to="/">首页</XtxBreadItem>
        <XtxBreadItem to="/cart">购物车</XtxBreadItem>
        <XtxBreadItem>支付订单</XtxBreadItem>
      </XtxBread>
      <!-- 付款信息 -->
      <!-- 付款信息 -->
      <div class="pay-info">
        <span class="icon iconfont icon-queren2"></span>
        <!--1.待付款-->
        <div class="tip" v-if="order.orderState === 1">
          <p>订单提交成功！请尽快完成支付。</p>
          <p>
            支付还剩 <span>{{ countTimeText }}</span
          >, 超时后将取消订单
          </p>
        </div>
        <!--2.已付款-->
        <div class="tip" v-if="order.orderState === 2">
          <p>订单已支付！</p>
        </div>
        <!--3.取消或超时-->
        <div class="tip" v-if="order.orderState === 6">
          <p>订单支付超时！</p>
        </div>
        <div class="amount">
          <span>应付总额：</span>
          <span>¥{{ order.payMoney }}</span>
        </div>
      </div>
      <!-- 付款方式 -->
      <div class="pay-type">
        <p class="head">选择以下支付方式付款</p>
        <div class="item">
          <p>支付平台</p>
          <a class="btn wx" href="javascript:;"></a>
          <a class="btn alipay" :href="payUrl"></a>
        </div>
        <div class="item">
          <p>支付方式</p>
          <a class="btn" href="javascript:;">招商银行</a>
          <a class="btn" href="javascript:;">工商银行</a>
          <a class="btn" href="javascript:;">建设银行</a>
          <a class="btn" href="javascript:;">农业银行</a>
          <a class="btn" href="javascript:;">交通银行</a>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import { ref } from 'vue'
import { useRoute } from 'vue-router'
import { findOrder } from '@/api/order'
import { useCountDown } from '@/hooks/countDown'

export default {
  name: 'XtxPayPage',
  setup () {
    const order = ref({})
    const route = useRoute()
    // 调用倒计时函数
    // start 方法传入秒数，开启倒计时 2.countTimeText倒计时分秒值(响应变量)
    const { start, countTimeText } = useCountDown()
    const getOrder = async () => {
      const res = await findOrder(route.query.orderId)
      // orderState - 订单状态，
      // 1为待付款、2为待发货(已付款)、3为待收货、4为待评价、5为已完成、6为已取消或超时

      order.value = res
      //  待付款状态执行倒计时
      order.value.orderState === 1 && start(order.value.countdown)
    }
    getOrder()

    // 准备支付跳转地址

    // 支付地址
    // const payUrl = '后台服务基准地址+支付页面地址+订单ID+回跳地址'
    const baseURL = 'http://pcapi-xiaotuxian-front-devtest.itheima.net/'
    // encodeURIComponent 编码后url作为参数(避免乱码)
    // redirectUrl 支付成功回跳页面=》显示支付结果
    const redirectUrl = encodeURIComponent('http://www.corho.com:8080/#/pay/callback')
    // const redirectUrl = 'http://www.corho.com:8080/#/pay/callback'
    // 跳转支付地址 = 1.后台接口地址 2.支付成功回调页面地址 3.参数:订单号
    const payUrl = `${baseURL}pay/aliPay?orderId=${route.query.orderId}&redirect=${redirectUrl}`

    console.log(payUrl)
    return { order, countTimeText, payUrl }
  }
}
</script>
<style scoped lang="less">
.pay-info {
  background: #fff;
  display: flex;
  align-items: center;
  height: 240px;
  padding: 0 80px;

  .icon {
    font-size: 80px;
    color: #1dc779;
  }

  .tip {
    padding-left: 10px;
    flex: 1;

    p {
      &:first-child {
        font-size: 20px;
        margin-bottom: 5px;
      }

      &:last-child {
        color: #999;
        font-size: 16px;
      }
    }
  }

  .amount {
    span {
      &:first-child {
        font-size: 16px;
        color: #999;
      }

      &:last-child {
        color: @priceColor;
        font-size: 20px;
      }
    }
  }
}

.pay-type {
  margin-top: 20px;
  background-color: #fff;
  padding-bottom: 70px;

  p {
    line-height: 70px;
    height: 70px;
    padding-left: 30px;
    font-size: 16px;

    &.head {
      border-bottom: 1px solid #f5f5f5;
    }
  }

  .btn {
    width: 150px;
    height: 50px;
    border: 1px solid #e4e4e4;
    text-align: center;
    line-height: 48px;
    margin-left: 30px;
    color: #666666;
    display: inline-block;

    &.active,
    &:hover {
      border-color: @xtxColor;
    }

    &.alipay {
      background: url(https://cdn.cnbj1.fds.api.mi-img.com/mi-mall/7b6b02396368c9314528c0bbd85a2e06.png) no-repeat center / contain;
    }

    &.wx {
      background: url(https://cdn.cnbj1.fds.api.mi-img.com/mi-mall/c66f98cff8649bd5ba722c2e8067c6ca.jpg) no-repeat center / contain;
    }
  }
}
</style>
