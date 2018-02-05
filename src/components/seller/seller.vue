<template>
  <div class="seller" ref="seller">
    <div class="seller-content">
      <div class="overview">
        <div class="overview-header">
          <h1 class="name">{{seller.name}}</h1>
          <div class="score-wrapper border-1px">
            <star class="star" :size="36" :score="seller.score"></star>
            <span class="num">（{{seller.ratingCount}}）</span>
            <span class="count">月售{{seller.sellCount}}单</span>
          </div>
        </div>
        <div class="summary">
          <div class="min-price">
            <h2 class="text">起送价</h2>
            <span class="num">
              {{seller.minPrice}}
            </span>
            <span class="unit">元</span>
          </div>
          <div class="delivery-price">
            <h2 class="text">商家配送</h2>
            <span class="num">
              {{seller.deliveryPrice}}
            </span>
            <span class="unit">元</span>
          </div>
          <div class="delivery-time">
            <h2 class="text">平均配送时间</h2>
            <span class="num">
              {{seller.deliveryTime}}
            </span>
            <span class="unit">分钟</span>
          </div>
        </div>
      </div>
      <split></split>
      <div class="bulletin-wrapper">
        <h1 class="title">公告与活动</h1>
        <div class="desc-wrapper border-1px">
          <p class="desc">{{seller.bulletin}}</p>
        </div>
        <ul>
          <li v-for="(support,index) in seller.supports" :key="index" class="support border-1px">
            <span class="icon" :class="supports_map[support.type]"></span>
            <span class="desc">{{support.description}}</span>
          </li>
        </ul>
      </div>
      <split></split>
      <div class="senery">
        <h1 class="title">商家实景</h1>
        <div class="pics-wrapper" ref="pics">
          <div class="pics" ref="picList">
            <img class="pic" width="120" height="90" v-for="(pic,index) in seller.pics" :key="index" :src="pic">
          </div>
          <!-- <ul class="pic-list" ref="picList">
            <li class="pic-item" v-for="(pic,index) in seller.pics" :key="index">
              <img :src="pic" width="120" height="90">
            </li>
          </ul> -->
        </div>
      </div>
      <split></split>
      <div class="infos">
        <h1 class="title border-1px">商家信息</h1>
        <div class="info-wrapper">
          <div class="info border-1px" v-for="(info,index) in seller.infos" :key="index">
            <p class="text">{{info}}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import split from '@/components/split/split'
import star from '@/components/star/star'
import BScroll from 'better-scroll'
export default {
  data() {
    return {
      supports_map: ['decrease', 'discount', 'special', 'invoice', 'guarantee']
    }
  },
  methods: {
    _initScroll() {
      if (!this.allScroll) {
        this.allScroll = new BScroll(this.$refs.seller)
      } else {
        this.allScroll.refresh()
      }
    },
    _initPics() {
      if (this.seller.pics) {
        let picWidth = 120
        let margin = 6
        let width = (picWidth + margin) * this.seller.pics.length - margin
        this.$refs.picList.style.width = width + 'px'
        this.$nextTick(() => {
          if (!this.picScroll) {
            this.picScroll = new BScroll(this.$refs.pics, {
              scrollX: true,
              eventPassthrough: 'vertical'
            })
          } else {
            this.picScroll.refresh()
          }
        })
      }
      console.log('init picScroll')
      console.log(this.$refs.pics)
    }
  },
  props: {
    seller: {
      type: Object
    }
  },
  components: {
    split,
    star
  },
  mounted() {
    this.$nextTick(() => {
      this._initScroll()
      this._initPics()
    })
  },
  watch: {
    seller() {
      this.$nextTick(() => {
        this._initScroll()
        this._initPics()
      })
    }
  }
}
</script>

<style lang="stylus">
@import '../../common/stylus/mixin'
.seller
  position absolute
  top 174px
  left 0
  bottom 0
  width 100%
  overflow hidden
  .overview
    .overview-header
      padding 18px 18px 0 18px
      .name
        font-size 14px
        // font-weight 700
        line-height 14px
        color rgb(7, 17, 27)
      .score-wrapper
        margin-top 8px
        font-size 0
        padding-bottom 18px
        border-1px(rgba(7, 17, 27, 0.1))
        .star
          display inline-block
          vertical-align top
        .num
          display inline-block
          vertical-align top
          margin-left 8px
          font-size 10px
          line-height 18px
        .count
          display inline-block
          vertical-align top
          margin-left 12px
          font-size 10px
          line-height 18px
          color rgb(77, 85, 93)
    .summary
      display flex
      padding 18px 0
      > div
        flex 1
        text-align center
        font-size 0
        > .text
          font-size 10px
          line-height 10px
          color rgb(147, 153, 159)
        > .num
          display inline-block
          margin-top 4px
          font-size 24px
          line-height 24px
          font-weight 200
          color rgb(7, 17, 27)
        > .unit
          display inline-block
          font-size 10px
          line-height 24px
          color rgb(7, 17, 27)
      .min-price
        border-right 1px solid rgba(7, 17, 27, 0.1)
      .delivery-price
        border-right 1px solid rgba(7, 17, 27, 0.1)
      .delivery-time
        flex 1
  .bulletin-wrapper
    padding 18px
    .title
      font-size 14px
      // font-weight 700
      line-height 14px
      color rgb(7, 17, 27)
    .desc-wrapper
      padding 16px 12px
      border-1px(rgba(7, 17, 27, 0.1))
      .desc
        font-size 12px
        line-height 24px
        font-weight 200
        color rgb(240, 20, 20)
    .support
      padding 16px 12px
      font-size 0
      &:last-child
        border-none()
        padding-bottom 0
      border-1px(rgba(7, 17, 27, 0.1))
      .icon
        display inline-block
        vertical-align top
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
      .desc
        display inline-block
        margin-left 6px
        font-size 12px
        font-weight 200
        line-height 16px
        color rgb(7, 17, 27)
  .senery
    padding 18px 0 18px 18px
    .title
      font-size 14px
      line-height 14px
      color rgb(7, 17, 27)
    .pics-wrapper
      overflow hidden
      width 100%
      .pics
        display flex
        flex-wrap nowrap
        margin-top 12px
        .pic
          flex 0 0 120px
          width 120px
          height 90px
          margin-right 6px
          &:last-child
            margin-right 0
  .infos
    padding 16px
    padding-bottom 0
    .title
      padding-bottom 12px
      font-size 14px
      line-height 14px
      color rgb(7, 17, 27)
      border-1px(rgba(7, 17, 27, 0.1))
    .info-wrapper
      .info
        padding 16px 12px
        border-1px(rgba(7, 17, 27, 0.1))
        &:last-child
          border-none()
        .text
          font-size 12px
          font-weight 200
          line-height 16px
          color rgb(7, 17, 27)
</style>
