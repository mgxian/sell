<template>
  <div class="goods">
    <div class="menu" ref="menuWrapper">
      <ul>
        <li v-for="(memuItem,index) in goods" :key="index" class="menu-item" :class="{'current':currentIndex===index}" @click="selectMenu(index,$event)" ref="menuList">
          <span class="text border-1px">
            <span v-show="memuItem.type>0" class="icon" :class="supports_map[memuItem.type]"></span>{{memuItem.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" ref="foodWrapper">
      <ul>
        <li v-for="(item,index) in goods" :key="index" class="food-list" ref="foodList">
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li v-for="(food, index) in item.foods" :key="index" class="food-item border-1px">
              <div class="icon">
                <img width="57" height="57" :src="food.icon">
              </div>
              <div class="content">
                <h2 class="name">{{food.name}}</h2>
                <p class="desc">{{food.description}}</p>
                <div class="extra">
                  <span class="count">月售{{food.sellCount}}份</span>
                  <span class="rating">好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="now">￥{{food.price}}</span>
                  <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <shopcart></shopcart>
  </div>
</template>

<script>
import BScroll from 'better-scroll'
import shopcart from '@/components/shopcart/shopcart'
const STATUS_OK = 0
export default {
  data() {
    return {
      goods: [],
      listHeight: [],
      scrollY: 0
    }
  },
  mounted() {},
  components: {
    shopcart
  },
  methods: {
    selectMenu(index, event) {
      if (!event._constructed) {
        return
      }
      let foodList = this.$refs.foodList
      let el = foodList[index]
      this.foodsScroll.scrollToElement(el, 300)
      this._calculateHeight()
    },
    _calculateHeight() {
      let foodList = this.$refs.foodList
      let height = 0
      this.listHeight.push(height)
      for (let i = 0; i < foodList.length; i++) {
        let item = foodList[i]
        height += item.clientHeight
        this.listHeight.push(height)
      }
    },
    _followScroll(index) {
      let menuList = this.$refs.menuList
      let el = menuList[index]
      this.menuScroll.scrollToElement(el, 300, 0, -100)
    },
    _initScroll() {
      if (!this.meunScroll) {
        console.log('init meunScroll')
        this.menuScroll = new BScroll(this.$refs.menuWrapper, {
          click: true
        })
      }

      if (!this.foodsScroll) {
        console.log('init foodsScroll')
        this.foodsScroll = new BScroll(this.$refs.foodWrapper, {
          click: true,
          probeType: 3
        })

        this.foodsScroll.on('scroll', pos => {
          // 判断滑动方向，避免下拉时分类高亮错误（如第一分类商品数量为1时，下拉使得第二分类高亮）
          if (pos.y <= 0) {
            this.scrollY = Math.abs(Math.round(pos.y))
          }
        })
      }
    }
  },
  computed: {
    currentIndex() {
      for (let i = 0; i < this.listHeight.length; i++) {
        let height1 = this.listHeight[i]
        let height2 = this.listHeight[i + 1]
        if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
          this._followScroll(i)
          return i
        }
      }
      return 0
    }
  },
  created() {
    this.supports_map = [
      'decrease',
      'discount',
      'special',
      'invoice',
      'guarantee'
    ]
    this.$http
      .get('/api/goods')
      .then(response => {
        let data = response.data
        if (data.errno === STATUS_OK) {
          this.goods = data.data
          this.$nextTick(() => {
            this._initScroll()
            this._calculateHeight()
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
.goods
  display flex
  position absolute
  top 174px
  bottom 46px
  width 100%
  overflow hidden
  .menu
    flex 0 0 80px
    background #f3f5f7
    .menu-item
      display table
      width 56px
      height 54px
      padding 0 12px
      line-height 14px
      &.current
        margin-top -1px
        background #fff
        .text
          font-weight 700
          border-none()
      .text
        display table-cell
        vertical-align middle
        width 56px
        font-size 12px
        border-1px(rgba(7, 17, 27, 0.1))
      .icon
        display inline-block
        vertical-align top
        width 12px
        height 12px
        margin-right 2px
        background-size 12px 12px
        background-repeat no-repeat
        &.decrease
          bg-image('decrease_3')
        &.discount
          bg-image('discount_3')
        &.guarantee
          bg-image('guarantee_3')
        &.invoice
          bg-image('invoice_3')
        &.special
          bg-image('special_3')
  .foods-wrapper
    flex 1
    .title
      font-size 12px
      color rgb(147, 153, 159)
      height 26px
      line-height 26px
      padding-left 14px
      background-color #f3f5f7
      border-left 2px solid #d9dde1
    .food-item
      display flex
      margin 18px
      padding-bottom 18px
      border-1px(rgba(7, 17, 27, 0.1))
      &:last-child
        border-none()
        margin-bottom 0
      .icon
        flex 0 0 57px
      .content
        flex 1
        margin-left 10px
        padding-top 2px
        color rgb(147, 153, 159)
        .name
          color rgb(7, 17, 27)
          font-size 14px
          height 14px
          line-height 14px
        .desc
          margin-top 8px
          font-size 10px
          line-height 12px
        .extra
          display flex
          margin-top 8px
          font-size 10px
          line-height 10px
          .count
            margin-right 12px
        .price
          display flex
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
</style>
