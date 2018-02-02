<template>
  <div class="shopcart">
    <div class="content">
      <div class="content-left">
        <div class="logo-wrapper">
          <div class="logo">
            <span class="icon-shopping_cart"></span>
          </div>
        </div>
        <div class="price">￥0</div>
        <div class="desc">另需配送费￥4元</div>
      </div>
      <div class="content-right">
        <div class="pay">￥20元起送</div>
      </div>
    </div>
  </div>
</template>

<script>
export default {}
</script>

<style lang="stylus">
.shopcart
  position fixed
  height 48px
  width 100%
  bottom 0
  left 0
  z-index 50
  background #141d27
  .content
    display flex
    font-size 0
    .content-left
      flex 1
      .logo-wrapper
        display inline-block
        position relative
        top -10px
        width 56px
        height 56px
        margin 0 12px
        padding 6px
        box-sizing border-box
        vertical-align top
        border-radius 50%
        background #141d27
        .logo
          text-align center
          width 100%
          height 100%
          border-radius 50%
          background #2b343c
          .icon-shopping_cart
            font-size 24px
            line-height 44px
            color: #80858a
      .price
        display inline-block
        vertical-align top
        margin-top 12px
        padding-right 12px
        line-height 24px
        font-size 16px
        font-weight 700
        color rgba(255, 255, 255, 0.4)
        border-right 1px solid rgba(255, 255, 255, 0.1)
      .desc
        display inline-block
        vertical-align top
        margin 12px 0 0 12px
        font-size 10px
        line-height 24px
        color rgba(255, 255, 255, 0.4)
    .content-right
      flex 0 0 105px
      .pay
        height 48px
        line-height 48px
        text-align center
        font-size 12px
        font-weight 700
        background #2b333b
        color rgba(255, 255, 255, 0.4)
</style>
