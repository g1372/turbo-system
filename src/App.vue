<template>
  <div id="app">
    <div id="nav">
      <!-- 上面的内容 -->
      <router-view></router-view>
      <!-- 底部导航 -->
      <FooterNav v-show="$route.meta.isnotlogin"></FooterNav>
    </div>
  </div>
</template>
<script>
// 组建引用
import FooterNav from "@/components/FooterNav/FooterNavss.vue";
import {mapActions} from "vuex"
export default {
  // 注册到components
  components: {
    FooterNav,
  },
  async mounted() {
    this.$store.dispatch("getAddress");
    this.$store.dispatch("getShops");
    // 存入商品分类
    this.$store.dispatch("getCategorys");
  
    this.getUserInfo();
  },
  methods: {
    ...mapActions(['getUserInfo'])
  }
};
</script>
<style lang="stylus">
@import url('./assets/stylus/reset.styl');
</style>
