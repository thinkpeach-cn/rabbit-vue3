<template>
  <div class="xtx-pay-checkout-page">
    <div class="container">
      <XtxBread>
        <XtxBreadItem to="/">首页</XtxBreadItem>
        <XtxBreadItem to="/cart">购物车</XtxBreadItem>
        <XtxBreadItem>填写订单</XtxBreadItem>
      </XtxBread>
      <div class="wrapper" v-if="orderInfo.summary">
        <!-- 收货地址 -->
        <h3 class="box-title">收货地址</h3>
        <div class="box-body">
          <div class="address">
            <div class="text">
              <!-- <div class="none">您需要先添加收货地址才可提交订单。</div> -->
              <ul>
                <li><span>收<i/>货<i/>人：</span>朱超</li>
                <li><span>联系方式：</span>132****2222</li>
                <li><span>收货地址：</span>海南省三亚市解放路108号物质大厦1003室</li>
              </ul>
            </div>
            <div class="action">
              <XtxButton class="btn">切换地址</XtxButton>
              <XtxButton class="btn">添加地址</XtxButton>
            </div>
          </div>
        </div>
        <!-- 商品信息 -->
        <h3 class="box-title">商品信息</h3>
        <div class="box-body">
          <table class="goods">
            <thead>
            <tr>
              <th width="520">商品信息</th>
              <th width="170">单价</th>
              <th width="170">数量</th>
              <th width="170">小计</th>
              <th width="170">实付</th>
            </tr>
            </thead>
            <tbody>
            <!--订单商品列表(渲染)-->
            <tr v-for="good in orderInfo.goods" :key="good.skuId">
              <td>
                <a href="javascript:;" class="info">
                  <img :src="good.picture" alt="">
                  <div class="right">
                    <p>{{ good.name }}</p>
                    <p>{{ good.attrsText }}</p>
                  </div>
                </a>
              </td>
              <!--单价-->
              <td>&yen;{{ good.price }}</td>
              <td>{{ good.count }}</td>
              <td>&yen;{{ good.totalPrice }}</td>
              <td>&yen;{{ good.totalPayPrice }}</td>
            </tr>
            </tbody>
          </table>
        </div>
        <!-- 配送时间 -->
        <h3 class="box-title">配送时间</h3>
        <div class="box-body">
          <a class="my-btn active" href="javascript:;">不限送货时间：周一至周日</a>
          <a class="my-btn" href="javascript:;">工作日送货：周一至周五</a>
          <a class="my-btn" href="javascript:;">双休日、假日送货：周六至周日</a>
        </div>
        <!-- 支付方式 -->
        <h3 class="box-title">支付方式</h3>
        <div class="box-body">
          <a class="my-btn active" href="javascript:;">在线支付</a>
          <a class="my-btn" href="javascript:;">货到付款</a>
          <span style="color:#999">货到付款需付5元手续费</span>
        </div>
        <!-- 金额明细(渲染) -->
        <h3 class="box-title">金额明细</h3>
        <div class="box-body">
          <div class="total">
            <dl>
              <dt>商品件数：</dt>
              <dd>{{ orderInfo.summary.goodsCount }}件</dd>
            </dl>
            <dl>
              <dt>商品总价：</dt>
              <dd>¥{{ orderInfo.summary.totalPrice }}</dd>
            </dl>
            <dl>
              <dt>运<i></i>费：</dt>
              <dd>¥{{ orderInfo.summary.postFee }}</dd>
            </dl>
            <dl>
              <dt>应付总额：</dt>
              <dd class="price">¥{{ orderInfo.summary.totalPayPrice }}</dd>
            </dl>
          </div>
        </div>
        <!-- 提交订单 -->
        <div class="submit">
          <XtxButton type="primary" @click="create">提交订单</XtxButton>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import { ref } from 'vue'
import { createOrder, findCheckoutInfo } from '@/api/order'
import { useRouter } from 'vue-router'
import { useStore } from 'vuex'

export default {
  name: 'XtxPayCheckoutPage',
  setup () {
    // 获取下单信息
    const orderInfo = ref({})
    const getOrderInfo = async () => {
      const res = await findCheckoutInfo()
      orderInfo.value = res
    }
    getOrderInfo()

    // 创建订单
    const router = useRouter()
    const store = useStore()
    const create = async () => {
      // 1.请求参数
      const data = {
        deliveryTimeType: 1,
        payType: 1,
        buyerMessage: '',
        addressId: '1580031175303368706', // 地址id(测试)
        goods: [] // {skuId, count}}
      }
      data.goods = orderInfo.value.goods.map(({ skuId, count }) => {
        return { skuId, count }
      })
      // 2. 调用接口创建订单
      const res = await createOrder(data)
      /*
      * 订单创建成功之后:
      * 1.跳转到支付页面，同时携带创建成功的订单id
      * 2.重新获取购物车数据(因为订单创建成功之后，后台会从购物车列表中删除下单商品)
      *
      * */
      router.push(`/pay?orderId=${res.id}`)
      store.dispatch('cart/getListAction')
    }
    return { orderInfo, create }
  }
}
</script>
<style scoped lang="less">
.xtx-pay-checkout-page {
  .wrapper {
    background: #fff;
    padding: 0 20px;

    .box-title {
      font-size: 16px;
      font-weight: normal;
      padding-left: 10px;
      line-height: 70px;
      border-bottom: 1px solid #f5f5f5;
    }

    .box-body {
      padding: 20px 0;
    }
  }
}

.address {
  border: 1px solid #f5f5f5;
  display: flex;
  align-items: center;

  .text {
    flex: 1;
    min-height: 90px;
    display: flex;
    align-items: center;

    .none {
      line-height: 90px;
      color: #999;
      text-align: center;
      width: 100%;
    }

    > ul {
      flex: 1;
      padding: 20px;

      li {
        line-height: 30px;

        span {
          color: #999;
          margin-right: 5px;

          > i {
            width: 0.5em;
            display: inline-block;
          }
        }
      }
    }

    > a {
      color: @xtxColor;
      width: 160px;
      text-align: center;
      height: 90px;
      line-height: 90px;
      border-right: 1px solid #f5f5f5;
    }
  }

  .action {
    width: 420px;
    text-align: center;

    .btn {
      width: 140px;
      height: 46px;
      line-height: 44px;
      font-size: 14px;

      &:first-child {
        margin-right: 10px;
      }
    }
  }
}

.goods {
  width: 100%;
  border-collapse: collapse;
  border-spacing: 0;

  .info {
    display: flex;
    text-align: left;

    img {
      width: 70px;
      height: 70px;
      margin-right: 20px;
    }

    .right {
      line-height: 24px;

      p {
        &:last-child {
          color: #999;
        }
      }
    }
  }

  tr {
    th {
      background: #f5f5f5;
      font-weight: normal;
    }

    td, th {
      text-align: center;
      padding: 20px;
      border-bottom: 1px solid #f5f5f5;

      &:first-child {
        border-left: 1px solid #f5f5f5;
      }

      &:last-child {
        border-right: 1px solid #f5f5f5;
      }
    }
  }
}

.my-btn {
  width: 228px;
  height: 50px;
  border: 1px solid #e4e4e4;
  text-align: center;
  line-height: 48px;
  margin-right: 25px;
  color: #666666;
  display: inline-block;

  &.active, &:hover {
    border-color: @xtxColor;
  }
}

.total {
  dl {
    display: flex;
    justify-content: flex-end;
    line-height: 50px;

    dt {
      i {
        display: inline-block;
        width: 2em;
      }
    }

    dd {
      width: 240px;
      text-align: right;
      padding-right: 70px;

      &.price {
        font-size: 20px;
        color: @priceColor;
      }
    }
  }
}

.submit {
  text-align: right;
  padding: 60px;
  border-top: 1px solid #f5f5f5;
}
</style>
