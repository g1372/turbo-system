<template>
  <div>
    <link rel="stylesheet" href="//at.alicdn.com/t/font_2085780_ncj58pfpkrc.css" />

    <!-- 底部导航 -->
    <footer class="footer_guide border-1px" >
      <router-link to="/msite" class="guide_item" :class="{on:$route.path=='/msite'}">
        <span class="item_icon">
          <i class="iconfont icon-iconfont27"></i>
        </span>
        <span>外卖</span>
      </router-link>
      <router-link to="/search" class="guide_item" :class="{on:$route.path=='/search'}">
        <span class="item_icon">
          <i class="iconfont icon-iconfontsearch"></i>
        </span>
        <span>搜索</span>
      </router-link>
      <router-link to="/order" class="guide_item" :class="{on:$route.path=='/order'}">
        <span class="item_icon">
          <i class="iconfont icon-iconfontlou"></i>
        </span>
        <span>订单</span>
      </router-link>
      <router-link to="/profile" class="guide_item" :class="{on:$route.path=='/profile'}">
        <span class="item_icon">
          <i class="iconfont icon-iconfonticon-yonghu"></i>
        </span>
        <span>我的</span>
      </router-link>
    </footer>
  </div>
</template>

<script>
export default {};
</script>

<style lang="stylus">
@import '../../assets/stylus/mixins.styl';


.footer_guide { // footer
  top-border-1px(#e4e4e4);
  position: fixed;
  z-index: 100;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: #fff;
  width: 100%;
  height: 50px;
  display: flex;

  .guide_item {
    display: flex;
    flex: 1;
    text-align: center;
    flex-direction: column;
    align-items: center;
    margin: 5px;
    color: #999999;
    text-decoration: none;

    &.on {
      color: $dColor;
    }

    span {
      font-size: 12px;
      margin-top: 4px;
      margin-bottom: 2px;

      .iconfont {
        font-size: 18px;
      }
    }
  }
}
</style>
