<template>
  <div>
    <div class="shopcart">
      <div class="content" @click="toggleList">
        <div class="content-left">
          <div class="logo-wrapper">
            <div class="logo" :class="{'highlight':totalPrice}">
              <span class="icon-shopping_cart" :class="{'highlight':totalPrice}"></span>
            </div>
            <div class="num">{{totalCount}}</div>
          </div>
          <div class="price" :class="{'highlight':totalPrice}">￥{{totalPrice}}</div>
          <div class="desc">{{descText}}</div>
        </div>
        <div class="content-right">
          <div class="pay" :class="{'payClass':payFlag}">{{payText}}</div>
        </div>
      </div>
      <div class="ball-container">
        <div v-for="(ball,index) in balls" :key="index">
          <transition name="drop" @before-enter="beforeDrop" @enter="dropping" @after-enter="afterDrop">
            <div v-show="ball.show" class="ball">
              <div class="inner inner-hook"></div>
            </div>
          </transition>
        </div>
      </div>
      <transition name="fold">
        <div class="shopcart-list" v-show="listShow">
          <div class="list-header">
            <h1 class="title">购物车</h1>
            <span class="empty" @click="empty">清空</span>
          </div>
          <div class="list-content" ref="foodList">
            <ul>
              <li class="food" v-for="(food,index) in selectFoods" :key="index">
                <span class="name">{{food.name}}</span>
                <div class="price">
                  <span>￥{{food.price*food.count}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cartcontrol :food="food"></cartcontrol>
                </div>
              </li>
            </ul>
          </div>
        </div>
      </transition>
    </div>
    <transition name="fade">
      <div class="list-mask" @click="hideList" v-show="listShow"></div>
    </transition>
  </div>
</template>

<script>
import cartcontrol from '@/components/cartcontrol/cartcontrol'
import BScroll from 'better-scroll'
export default {
  data() {
    return {
      balls: [
        {
          show: false
        },
        {
          show: false
        },
        {
          show: false
        },
        {
          show: false
        },
        {
          show: false
        }
      ],
      dropBalls: [],
      fold: true
    }
  },
  props: {
    deliveryPrice: {
      type: Number,
      default: 0
    },
    minPrice: {
      type: Number,
      default: 0
    },
    selectFoods: {
      type: Array,
      default() {
        return [
          {
            price: 5,
            count: 50
          }
        ]
      }
    }
  },
  components: {
    cartcontrol
  },
  methods: {
    toggleList() {
      if (!this.totalCount) {
        return
      }
      this.fold = !this.fold
      // console.log(this.fold)
    },
    empty() {
      this.selectFoods.forEach(food => {
        food.count = 0
      })
      this.fold = true
    },
    hideList() {
      this.fold = true
    },
    drop(el) {
      for (let i = 0; i < this.balls.length; i++) {
        let ball = this.balls[i]
        if (!ball.show) {
          ball.show = true
          ball.el = el
          this.dropBalls.push(ball)
          // console.log('push')
          return
        }
      }
    },
    beforeDrop(el) {
      let count = this.balls.length
      while (count--) {
        let ball = this.balls[count]
        if (ball.show) {
          let rect = ball.el.getBoundingClientRect()
          let x = rect.left - 32
          let y = -(window.innerHeight - rect.top - 22)
          el.style.display = ''
          el.style.webkitTransform = `translate3d(0,${y}px,0)`
          el.style.transform = `translate3d(0,${y}px,0)`
          let inner = el.getElementsByClassName('inner-hook')[0]
          inner.style.webkitTransform = `translate3d(${x}px,0,0)`
          inner.style.transform = `translate3d(${x}px,0,0)`
        }
      }
      // console.log('beforeDrop')
    },
    dropping(el, done) {
      /* eslint-disable no-unused-vars */
      let offsetHeight = el.offsetHeight
      this.$nextTick(() => {
        el.style.webkitTransform = 'translate3d(0,0,0)'
        el.style.transform = 'translate3d(0,0,0)'
        let inner = el.getElementsByClassName('inner-hook')[0]
        inner.style.webkitTransform = 'translate3d(0,0,0)'
        inner.style.transform = 'translate3d(0,0,0)'
        el.addEventListener('transitionend', done)
      })
      // console.log('dropping')
    },
    afterDrop(el) {
      let ball = this.dropBalls.shift()
      if (ball) {
        ball.show = false
        el.style.display = 'none'
      }
      // console.log('afterDrop')
    },
    _initScroll() {
      if (!this.listScroll) {
        this.listScroll = new BScroll(this.$refs.foodList, {
          click: true
        })
      } else {
        this.listScroll.refresh()
      }
    }
  },
  computed: {
    descText() {
      return `另需配送费￥${this.deliveryPrice}元`
    },
    payFlag() {
      return this.totalPrice - this.minPrice >= 0
    },
    payText() {
      let total = this.totalPrice
      let diffPrice = this.minPrice - total
      if (total === 0) {
        return `￥${this.minPrice}元起送`
      } else if (diffPrice > 0) {
        return `还差￥${diffPrice}元起送`
      } else {
        return `去结算`
      }
    },
    totalCount() {
      let total = 0
      this.selectFoods.forEach(food => {
        total += food.count
      })
      return total
    },
    totalPrice() {
      let total = 0
      this.selectFoods.forEach(food => {
        total += food.price * food.count
      })
      return total
    },
    listShow() {
      if (!this.totalCount) {
        return false
      }
      let show = !this.fold
      if (show) {
        this.$nextTick(() => {
          this._initScroll()
        })
      }
      return show
    }
  }
}
</script>

<style lang="stylus">
@import '../../common/stylus/mixin'
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
    background #141d27
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
          &.highlight
            background rgb(0, 160, 220)
          .icon-shopping_cart
            font-size 24px
            line-height 44px
            color #80858a
            &.highlight
              color #fff
        .num
          position absolute
          top 0
          right 0
          display inline-block
          text-align center
          width 24px
          height 16px
          line-height 16px
          font-size 9px
          font-weight 700
          color rgb(255, 255, 255)
          background red
          border-radius 16px
          box-shadow 0 4px 8px 0 rgba(0, 0, 0, 0.4)
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
        &.highlight
          color #fff
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
        &.payClass
          background #00b43c
          color #fff
  .ball-container
    .ball
      position fixed
      left 32px
      bottom 22px
      z-index 200
      transition all 0.4s cubic-bezier(0.49, -0.29, 0.75, 0.41)
      .inner
        width 16px
        height 16px
        border-radius 50%
        background rgb(0, 160, 220)
        transition all 0.4s linear
  .shopcart-list
    position absolute
    left 0
    top 0
    width 100%
    z-index -1
    transform translate3d(0, -100%, 0)
    &.fold-enter-active, &.fold-leave-active
      transition all 0.5s
    &.fold-enter, &.fold-leave-to
      transform translate3d(0, 0, 0)
    .list-header
      background #f3f5f7
      padding 0 18px
      height 40px
      color rgb(7, 17, 27)
      line-height 40px
      border-bottom 1px solid rgba(7, 17, 27, 0.1)
      .title
        float left
        font-size 14px
      .empty
        float right
        font-size 12px
        color rgb(0, 160, 220)
    .list-content
      background #fff
      padding 0 18px
      max-height 217px
      overflow hidden
      .food
        padding 12px 0
        box-sizing border-box
        border-1px(rgba(7, 17, 27, 0.1))
        &:last-child
          border-none()
        .name
          display inline-block
          font-size 14px
          line-height 24px
          color rgb(7, 17, 27)
        .price
          position absolute
          right 90px
          bottom 12px
          font-size 14px
          font-weight 700
          line-height 24px
          color red
          > span
            font-weight 700
        .cartcontrol-wrapper
          position absolute
          right 0
          bottom 6px
.list-mask
  position fixed
  top 0
  left 0
  width 100%
  height 100%
  z-index 40
  backdrop-filter blur(10px)
  opacity 1
  background rgba(7, 17, 27, 0.6)
  &.fade-enter-active, &.fade-leave-active
    transition all 0.5s
  &.fade-enter, &.fade-leave-to
    opacity 0
    background rgba(7, 17, 27, 0)
</style>
