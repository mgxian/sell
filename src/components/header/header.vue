<template>
  <div class="header">
    <div class="content-wrapper">
      <div class="avatar">
        <img :src="seller.avatar" width="64" height="64">
      </div>
      <div class="content">
        <div class="title">
          <span class="brand"></span>
          <span class="name">{{seller.name}}</span>
        </div>
        <div class="desc">
          {{seller.description}}/{{seller.deliveryTime}}分钟送达
        </div>
        <div v-if="seller.supports" class="suport">
          <span class="icon" :class="supports_map[seller.supports[0].type]"></span>
          <span class="text">{{seller.supports[0].description}}</span>
        </div>
      </div>
      <div v-if="seller.supports" class="support-count" @click="showDetail">
        <span class="count">{{seller.supports.length}}个</span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <div class="bulletin-wrapper">
      <span class="title"></span><!--
      --><span class="text">{{seller.bulletin}}</span>
      <i class="icon-keyboard_arrow_right"></i>
    </div>
    <div class="background">
      <img :src="seller.avatar" width="100%" height="100%">
    </div>
    <div v-show="detailShow" class="detail">
      <div class="detail-wrapper clearfix">
        <div class="detail-main">
          <h1 class="name">{{seller.name}}</h1>
          <div class="star-wrapper">
            <star :size="48" :score="seller.score"></star>
          </div>
          <div v-if="seller.supports" class="title">
            <div class="line"></div>
            <div class="text">优惠信息</div>
            <div class="line"></div>
          </div>
          <ul v-if="seller.supports" class="suport">
            <li v-for="(suport,index) in seller.supports" :key="index" class="suport-item">
              <span class="icon" :class="supports_map[suport.type]"></span>
              <span class="text">{{suport.description}}</span>
            </li>
          </ul>
          <div class="title">
            <div class="line"></div>
            <div class="text">商家公告</div>
            <div class="line"></div>
          </div>
          <div class="bulletin">{{seller.bulletin}}</div>
        </div>
      </div>
      <div class="detail-close" @click="showDetail">
        <i class="icon-close"></i>
      </div>
    </div>
  </div>
</template>

<script>
import star from '@/components/star/star'
export default {
  data() {
    return {
      supports_map: ['decrease', 'discount', 'special', 'invoice', 'guarantee'],
      detailShow: false
    }
  },
  methods: {
    showDetail: function() {
      this.detailShow = !this.detailShow
    }
  },
  props: {
    seller: {
      type: Object,
      default() {
        return {}
      }
    }
  },
  components: {
    star
  }
}
</script>

<style lang="stylus">
@import '../../common/stylus/mixin'
.header
  position relative
  overflow hidden
  color rgb(255, 255, 255)
  background-color rgba(7, 17, 27, 0.5)
  .content-wrapper
    position relative
    padding 24px 12px 18px 24px
    font-size 0
    .avatar
      display inline-block
      vertical-align top
      img
        border-radius 2px
    .content
      display inline-block
      margin-left 16px
      .title
        margin 2px 0px 8px 0px
        .brand
          display inline-block
          vertical-align top
          width 30px
          height 18px
          bg-image('brand')
          background-size 30px 18px
          background-repeat no-repeat
        .name
          margin-left 6px
          font-size 16px
          line-height 18px
          font-weight bold
      .desc
        margin-top 8px
        font-size 12px
        line-height 12px
      .suport
        margin-top 10px
        .icon
          display inline-block
          vertical-align top
          width 12px
          height 12px
          background-size 12px 12px
          background-repeat no-repeat
          &.decrease
            bg-image('decrease_1')
          &.discount
            bg-image('discount_1')
          &.guarantee
            bg-image('guarantee_1')
          &.invoice
            bg-image('invoice_1')
          &.special
            bg-image('special_1')
        .text
          margin-left 4px
          font-size 10px
          line-height 12px
    .support-count
      position absolute
      bottom 14px
      right 12px
      padding 0 8px
      line-height 24px
      height 24px
      border-radius 14px
      background rgba(0, 0, 0, 0.2)
      text-align center
      .count
        font-size 10px
        vertical-align top
      .icon-keyboard_arrow_right
        line-height 24px
        font-size 10px
        margin-left 2px
  .bulletin-wrapper
    position relative
    height 28px
    line-height 28px
    padding 0 22px 0 12px
    white-space nowrap
    overflow hidden
    text-overflow ellipsis
    background-color rgba(7, 17, 27, 0.2)
    .title
      display inline-block
      vertical-align top
      margin-top 8px
      width 22px
      height 12px
      bg-image('bulletin')
      background-size 22px 12px
      background-repeat no-repeat
    .text
      vertical-align top
      margin 0 4px
      font-size 10px
    .icon-keyboard_arrow_right
      position absolute
      right 12px
      top 8px
      font-size 10px
  .background
    position absolute
    top 0
    left 0
    width 100%
    height 100%
    z-index -1
    filter blur(10px)
  .detail
    position fixed
    z-index 200
    top 0
    left 0
    width 100%
    height 100%
    overflow auto
    backdrop-filter: blur(10px)
    opacity: 1
    background rgba(7, 17, 27, 0.8)
    .detail-wrapper
      min-height 100%
      width 100%
      .detail-main
        margin-top 64px
        padding-bottom 64px
        .name
          text-align center
          font-size 16px
          font-weight 700
          line-height 16px
        .star-wrapper
          text-align center
          margin-top 18px
          padding 2px 0
        .title
          width 80%
          display flex
          padding 0 36px
          margin 28px auto 24px auto
          .line
            flex 1
            position relative
            top -6px
            border-bottom 1px solid rgba(255, 255, 255, 0.2)
          .text
            padding 0 12px
            font-weight 700
            font-size 14px
        .suport
          width 80%
          margin 0 auto
          .suport-item
            padding 0 12px
            margin-bottom 12px
            font-size 0
            &:last-child
              margin-bottom 0px
            .icon
              display inline-block
              vertical-align top
              margin-right 6px
              width 16px
              height 16px
              background-size 16px 16px
              background-repeat no-repeat
              &.decrease
                bg-image('decrease_2')
              &.discount
                bg-image('discount_2')
              &.guarantee
                bg-image('guarantee_2')
              &.invoice
                bg-image('invoice_2')
              &.special
                bg-image('special_2')
            .text
              display inline-block
              font-size 12px
              line-height 16px
        .bulletin
          width 80%
          margin 0 auto
          padding 0 12px
          font-size 12px
          line-height 24px
    .detail-close
      position relative
      width 32px
      height 32px
      margin -64px auto 0 auto
      clear both
      font-size 32px
</style>
