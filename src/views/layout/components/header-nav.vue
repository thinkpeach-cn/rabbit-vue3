<template>
  <ul class="app-header-nav">
    <li class="home"><RouterLink to="/">首页</RouterLink></li>
<!--有数据结构-->
    <template v-if="list.length">
    <li v-for="item in list" :key="item.id">
      <!--一级菜单(分类)-->
      <a href="#">{{item.name}}</a>
      <!-- hover 显示 start -->
      <div class="layer">
        <ul>
          <!--二级菜单-->
          <li v-for="child in item.children" :key="child.id">
            <a href="#">
              <img :src="child.picture" alt="">
              <p>{{child.name}}</p>
            </a>
          </li>
        </ul>
      </div>
      <!-- hover 显示 end -->
    </li>
    </template>
<!--没有数据，使用相同结构，里边用骨架屏填充内容(占位)-->
  <template v-else>
    <li v-for="item in 9" :key="item">
      <XtxSkeleton style="margin-right: 10px" :width="32" :height="32" bg="#ccc"/>
    </li>
  </template>
  </ul>
</template>

<script>
// import { mapState } from 'vuex'
import { useStore } from 'vuex'
import { computed } from 'vue'

export default {
  name: 'AppHeaderNav',
  // vue2实现
  // computed: {
  //   ...mapState('category', ['list'])
  // },
  // created () {
  //   this.$store.dispatch('category/getListAction')
  // }
  // vue3 实现
  setup () {
    // store === this.$store 都是vuex实例
    const store = useStore()
    // store.dispatch('category/getListAction')
    // 获取映射vuex中的数据
    const list = computed(() => {
      return store.state.category.list
    })
    return { list }
  }
}
</script>

<style lang='less' scoped >
.app-header-nav {
  width: 820px;
  display: flex;
  padding-left: 40px;
  position: relative;
  z-index: 998;
  > li {
    margin-right: 40px;
    width: 38px;
    text-align: center;
    > a {
      font-size: 16px;
      line-height: 32px;
      height: 32px;
      display: inline-block;
    }
    > a.router-link-exact-active {
      color: @xtxColor;
      border-bottom: 1px solid @xtxColor;
    }
    &:hover {
      > a {
        color: @xtxColor;
        border-bottom: 1px solid @xtxColor;
      }
    }
    // ++ 初始样式 不显示
    .layer {
      width: 1240px;
      background-color: #fff;
      position: absolute;
      left: -200px;
      top: 56px;
      height: 0;
      overflow: hidden;
      opacity: 0;
      box-shadow: 0 0 5px #ccc;
      transition: all 0.2s 0.1s;
      ul {
        display: flex;
        flex-wrap: wrap;
        padding: 0 70px;
        align-items: center;
        height: 124px;
        li {
          width: 110px;
          text-align: center;
          img {
            width: 60px;
            height: 60px;
          }
          p {
            padding-top: 10px;
          }
          &:hover {
            p {
              color: @xtxColor;
            }
          }
        }
      }
    }
    // hover之后显示出来
    &:hover {
      > a {
        color: @xtxColor;
        border-bottom: 1px solid @xtxColor;
      }
      > .layer {
        height: 120px;
        opacity: 1;
      }
    }
    //end
  }
}
</style>
