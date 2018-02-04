<template>
  <div>
    <transition name="move">
      <div class="food" v-show="showFlag" ref="food">
        <div class="food-content">
          <div class="image-header">
            <img :src="food.image">
            <div class="back" @click="hide">
              <i class="icon-arrow_lift"></i>
            </div>
          </div>
          <div class="content">
            <h1 class="title">{{food.name}}</h1>
            <div class="detail">
              <span class="count">月售{{food.sellCount}}份</span>
              <span class="rating">好评率{{food.rating}}%</span>
            </div>
            <div class="price">
              <span class="now">￥{{food.price}}</span>
              <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
            </div>
            <div class="cartcontrol-wrapper">
              <cartcontrol @add="addFood" :food="food"></cartcontrol>
            </div>
            <transition name="fade">
              <div @click="addFirst($event)" class="buy" v-show="!food.count||food.count===0">
                加入购物车
              </div>
            </transition>
          </div>
          <split v-show="food.info"></split>
          <div class="info" v-show="food.info">
            <h1 class="title">商品介绍</h1>
            <p class="text">{{food.info}}</p>
          </div>
          <split></split>
          <div class="rating">
            <h1 class="title">商品评价</h1>
            <ratingselect @select="selectRating" @toggle="toggleContent" :ratings="food.ratings" :selectType="selectType" :onlyContent="onlyContent" :desc="desc"></ratingselect>
            <div class="detail">
              <ul>
                <li v-show="needShow(rating.rateType,rating.text)" v-for="(rating,index) in food.ratings" :key="index" class="rating">
                  <div class="user">
                    <span class="name">{{rating.username}}</span>
                    <img class="avatar" width="12" height="12" :src="rating.avatar">
                  </div>
                  <div class="time">{{rating.rateTime | formatDate}}</div>
                  <p class="text">
                    <span :class="{'icon-thumb_up':rating.rateType===0,'icon-thumb_down':rating.rateType===1}"></span>{{rating.text}}
                  </p>
                </li>
              </ul>
            </div>
          </div>
        </div>
      </div>
    </transition>
  </div>
</template>
<script>
import BScroll from 'better-scroll'
import cartcontrol from '@/components/cartcontrol/cartcontrol'
import split from '@/components/split/split'
import ratingselect from '@/components/ratingselect/ratingselect'
import Vue from 'vue'
import { formatDate } from '@/common/js/date'

// const POSITIVE = 0
// const NEGATIVE = 1
const ALL = 2

export default {
  props: {
    food: {
      type: Object
    }
  },
  data() {
    return {
      showFlag: false,
      selectType: ALL,
      onlyContent: true,
      desc: {
        all: '全部',
        positive: '推荐',
        negative: '吐槽'
      }
    }
  },
  methods: {
    show() {
      this.showFlag = true
      this.$nextTick(() => {
        this._initScroll()
      })
    },
    hide() {
      this.showFlag = false
    },
    addFirst(event) {
      if (!event._constructed) {
        return
      }
      Vue.set(this.food, 'count', 1)
      this.$emit('add', event.target)
    },
    addFood(target) {
      this.$emit('add', target)
    },
    _initScroll() {
      if (!this.scroll) {
        // console.log('init meunScroll')
        this.scroll = new BScroll(this.$refs.food, {
          click: true
        })
      } else {
        this.scroll.refresh()
      }
    },
    needShow(rateType, text) {
      if (this.selectType === rateType || this.selectType === ALL) {
        if (this.onlyContent) {
          return text
        } else {
          return true
        }
      }
    },
    selectRating(type) {
      this.selectType = type
      this.$nextTick(() => {
        this.scroll.refresh()
      })
    },
    toggleContent() {
      this.onlyContent = !this.onlyContent
      this.$nextTick(() => {
        this.scroll.refresh()
      })
    }
  },
  computed: {},
  filters: {
    formatDate(time) {
      let date = new Date(time)
      return formatDate(date, 'yyyy-MM-dd hh:mm')
    }
  },
  components: {
    cartcontrol,
    split,
    ratingselect
  }
}
</script>
<style lang="stylus">
@import '../../common/stylus/mixin'
.food
  position fixed
  top 0
  left 0
  bottom 48px
  z-index 30
  width 100%
  background #fff
  transform translate3d(0, 0, 0)
  &.move-enter-active, &.move-leave-active
    transition all 0.2s linear
  &.move-enter, &.move-leave-to
    opacity 0
    transform translate3d(100%, 0, 0)
  .image-header
    position relative
    width 100%
    height 0
    padding-top 100%
    img
      position absolute
      top 0
      left 0
      width 100%
      height 100%
    .back
      position absolute
      top 10px
      left 0
      .icon-arrow_lift
        display block
        padding 10px
        font-size 20px
        color #fff
  .content
    position relative
    padding 18px
    .title
      font-size 14px
      font-weight 700
      color rgb(7, 17, 27)
      line-height 14px
      margin-bottom 8px
    .detail
      margin-bottom 18px
      height 10px
      line-height 10px
      font-size 0
      color rgb(147, 153, 159)
      .count, .rating
        font-size 10px
      .rating
        margin-left 12px
    .price
      display flex
      margin-top 18px
      line-height 24px
      > span
        font-weight 700
      .now
        font-size 14px
        color red
      .old
        text-decoration line-through
        margin-left 8px
        font-size 10px
        color rgb(147, 153, 159)
    .cartcontrol-wrapper
      position absolute
      right 12px
      bottom 12px
    .buy
      position absolute
      right 18px
      bottom 18px
      z-index 10
      height 24px
      line-height 24px
      padding 0 12px
      box-sizing border-box
      font-size 10px
      color #fff
      border-radius 12px
      background rgb(0, 160, 220)
      &.fade-enter-active, &.fade-leave-active
        transition all 0.2s
      &.fade-enter, &.fade-leave-active
        opacity 0
  .info
    padding 18px
    .title
      font-size 14px
      color rgb(7, 17, 27)
    .text
      margin-top 6px
      padding 0 8px
      font-size 12px
      color rgb(77, 85, 93)
      line-height 24px
  .rating
    padding-top 18px
    .title
      margin-left 18px
      font-size 14px
      color rgb(7, 17, 27)
    .detail
      padding 0 18px
      .rating
        position relative
        padding 16px 0
        border-1px(rgba(7, 17, 27, 0.1))
        .user
          position absolute
          right 0
          top 16px
          line-height 12px
          font-size 0
          .name
            display inline-block
            margin-right 6px
            vertical-align top
            font-size 10px
            color rgb(147, 153, 159)
          .avatar
            border-radius 50%
        .time
          margin-bottom 6px
          line-height 12px
          font-size 10px
          color rgb(147, 153, 159)
        .text
          line-height 16px
          font-size 12px
          color rgb(7, 17, 27)
          .icon-thumb_up, .icon-thumb_down
            margin-right 4px
            line-height 16px
            font-size 12px
          .icon-thumb_up
            color rgb(0, 160, 220)
          .icon-thumb_down
            color rgb(147, 153, 159)
</style>
