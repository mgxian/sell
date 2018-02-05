<template>
  <div class="ratings" ref="ratings">
    <div class="rating-content">
      <div class="summary">
        <div class="left">
          <h1 class="score">{{seller.score}}</h1>
          <h1 class="text">综合评分</h1>
          <p class="desc">高于周边商家{{seller.rankRate}}%</p>
        </div>
        <div class="right">
          <div class="service-score">
            <h1 class="text">服务态度</h1>
            <star class="star" :score="seller.serviceScore" :size="36"></star>
            <h1 class="num">{{seller.serviceScore}}</h1>
          </div>
          <div class="food-score">
            <h1 class="text">商品评分</h1>
            <star class="star" :score="seller.foodScore" :size="36"></star>
            <h1 class="num">{{seller.foodScore}}</h1>
          </div>
          <div class="delivery-time">
            <h1 class="text">送达时间</h1>
            <h2 class="time">{{seller.deliveryTime}}分钟</h2>
          </div>
        </div>
      </div>
      <split></split>
      <div class="rating">
        <ratingselect @select="selectRating" @toggle="toggleContent" :ratings="ratings" :selectType="selectType" :onlyContent="onlyContent" :desc="desc"></ratingselect>
        <div class="detail">
          <ul>
            <li v-show="needShow(rating.rateType,rating.text)" v-for="(rating,index) in ratings" :key="index" class="rating">
              <img class="avatar" width="28" height="28" :src="rating.avatar">
              <div class="content">
                <div class="user">
                  <span class="name">{{rating.username}}</span>
                  <div class="star-wrapper">
                    <star class="star" :score="rating.score" :size="24"></star>
                    <span v-show="rating.deliveryTime" class="delivery-time">{{rating.deliveryTime}}分钟送达</span>
                  </div>
                </div>
                <div class="time">{{rating.rateTime | formatDate}}</div>
                <p class="text">
                  {{rating.text}}
                </p>
                <div class="recommend">
                  <span :class="{'icon-thumb_up':rating.rateType===0,'icon-thumb_down':rating.rateType===1}"></span>
                  <span class="recommend-item" v-for="(recommendItem,index) in rating.recommend" :key="index">
                    {{recommendItem}}
                  </span>
                </div>
              </div>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import BScroll from 'better-scroll'
// import cartcontrol from '@/components/cartcontrol/cartcontrol'
import split from '@/components/split/split'
import ratingselect from '@/components/ratingselect/ratingselect'
import star from '@/components/star/star'
// import Vue from 'vue'
import { formatDate } from '@/common/js/date'

const ALL = 2
const STATUS_OK = 0
export default {
  data() {
    return {
      ratings: [],
      selectType: ALL,
      onlyContent: true,
      desc: {
        all: '全部',
        positive: '满意',
        negative: '不满意'
      }
    }
  },
  props: {
    seller: {
      type: Object
    }
  },
  methods: {
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
    },
    _initScroll() {
      this.scroll = new BScroll(this.$refs.ratings, {
        click: true
      })
    }
  },
  filters: {
    formatDate(time) {
      let date = new Date(time)
      return formatDate(date, 'yyyy-MM-dd hh:mm')
    }
  },
  components: {
    ratingselect,
    split,
    star
  },
  created() {
    this.$http
      .get('/api/ratings')
      .then(response => {
        let data = response.data
        if (data.errno === STATUS_OK) {
          this.ratings = data.data
          // console.log(this.ratings)
          this.$nextTick(() => {
            this._initScroll()
          })
        }
      })
      .catch(error => {
        console.log(error)
      })
  }
}
</script>

<style lang="stylus">
@import '../../common/stylus/mixin'
.ratings
  position absolute
  width 100%
  top 174px
  left 0
  bottom 0
  overflow hidden
  .summary
    display flex
    padding 18px 24px 18px 0
    .left
      flex 0 0 137px
      padding 6px 0
      text-align center
      border-right 1px solid rgba(147, 153, 159, 0.2)
      .score
        font-size 24px
        color rgb(255, 153, 0)
        line-height 28px
      .text
        margin-top 6px
        font-size 12px
        color rgb(7, 17, 27)
        line-height 12px
      .desc
        margin-top 8px
        font-size 10px
        color rgb(147, 153, 159)
        line-height 10px
    .right
      flex 1
      padding 6px 0 6px 24px
      .service-score, .food-score, .delivery-time
        font-size 0
        margin-bottom 8px
        .text
          display inline-block
          vertical-align top
          font-size 12px
          line-height 18px
          color rgb(7, 17, 27)
        .star
          display inline-block
          margin-left 12px
        .num
          display inline-block
          vertical-align top
          margin-left 12px
          font-size 12px
          line-height 18px
          color rgb(255, 153, 0)
      .delivery-time
        margin-bottom 0
        .time
          display inline-block
          margin-left 12px
          font-size 12px
          color rgb(147, 153, 159)
          line-height 18px
  .detail
    .rating
      display flex
      padding 18px 18px 18px 18px
      border-1px(rgba(7, 17, 27, 0.1))
      font-size 0
      .avatar
        flex 0 0 28px
        border-radius 50%
        margin-right 12px
      .content
        flex 1
        display inline-block
        vertical-align top
        .user
          font-size 0
          .name
            font-size 10px
            color rgb(7, 17, 27)
            line-height 12px
          .star-wrapper
            font-size 0
            .star
              display inline-block
              margin-top 4px
            .delivery-time
              display inline-block
              margin-left 6px
              font-size 10px
              line-height 12px
              color rgb(147, 153, 159)
        .time
          position absolute
          right 18px
          top 18px
          font-size 10px
          line-height 12px
          color rgb(147, 153, 159)
        .text
          margin-top 6px
          font-size 12px
          color rgb(7, 17, 27)
          line-height 18px
        .recommend
          margin-top 8px
          font-size 0
          .icon-thumb_up
            margin 0 8px 4px 0
            font-size 12px
            line-height 16px
            color rgb(0, 160, 220)
          .icon-thumb_down
            margin 0 8px 4px 0
            font-size 12px
            line-height 16px
            color rgb(183, 187, 191)
          .recommend-item
            display inline-block
            padding 0 6px
            margin 0 8px 4px 0
            font-size 9px
            line-height 16px
            color rgb(147, 153, 159)
            border 1px solid rgba(7, 17, 27, 0.1)
            border-radius 1px
</style>
