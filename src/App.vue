<template>
  <div id="app">
    <v-header :seller="seller"></v-header>
    <div class="tab border-1px">
      <div class="tab-item">
        <router-link to="/goods">商品</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/ratings">评论</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/seller">商家</router-link>
      </div>
    </div>
    <div class="content">
      <keep-alive>
        <router-view :seller="seller"></router-view>
      </keep-alive>
    </div>
  </div>
</template>

<script>
import header from '@/components/header/header'

const STATUS_OK = 0
export default {
  data() {
    return {
      seller: {}
    }
  },
  created() {
    this.$http
      .get('/api/seller')
      .then(response => {
        let data = response.data
        if (data.errno === STATUS_OK) {
          this.seller = data.data
        }
        // console.log(this.seller)
      })
      .catch(error => {
        console.log(error)
      })
  },
  components: {
    'v-header': header
  }
}
</script>

<style lang="stylus">
@import './common/stylus/mixin.styl'
#app
  .tab
    display flex
    width 100%
    height 40px
    line-height 40px
    border-1px(rgba(7, 17, 27, 0.1))
    .tab-item
      flex 1
      text-align center
      & > a
        display block
        font-size 14px
        color rgb(77, 85, 93)
        &.router-link-active
          color rgb(240, 20, 20)
</style>
