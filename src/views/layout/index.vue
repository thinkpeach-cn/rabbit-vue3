<template>
  <XtxTopnav/>
  <XtxHeader/>
<!--吸顶-->
  <XtxHeaderSticky/>
  <main>
    <!--二级路由出口 =>首页、商品详情、购物车等组件都在这里渲染-->
    <router-view></router-view>
  </main>
  <xtx-footer/>
</template>

<script>
import XtxTopnav from './components/topnav'
import XtxHeader from '@/views/layout/components/header'
import XtxFooter from '@/views/layout/components/footer'
// 吸顶组件
import XtxHeaderSticky from '@/views/layout/components/header-sticky'
import { useStore } from 'vuex'
export default {
  name: 'xtx-layout',
  components: {
    XtxTopnav,
    XtxHeader,
    XtxFooter,
    XtxHeaderSticky
  },
  setup () {
    // 在父组件获取菜单数据
    const store = useStore()
    store.dispatch('category/getListAction')
  }
}
</script>

<style scoped lang='less'></style>
