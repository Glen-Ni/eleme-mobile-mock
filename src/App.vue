<template>
  <div id="app">
    <v-header :seller="seller"></v-header>
    <div class="tab border-1px">
      <div class="tab-item">
        <router-link class="r-link" to="/goods">商品</router-link>
      </div>
      <div class="tab-item">
        <router-link class="r-link" to="/ratings">评论</router-link>
      </div>
      <div class="tab-item">
        <router-link class="r-link" to="/seller">商家</router-link>
      </div>
    </div>
    <router-view :seller="seller"></router-view>
  </div>
</template>

<script>
  import header from './components/header/header'

  const ERR_OK = 0

  export default {
    data () {
      return {
        seller: {}
      }
    },
    created () {
      this.$http.get('/apis/seller').then((response) => {
        response = response.body
        // console.log(response)
        if (response.errno === ERR_OK) {
          this.seller = response.data
          // console.log(this.seller)
        }
      })
    },
    name: 'App',
    components: {
      'v-header': header
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "./common/stylus/mixin.styl"
  #app
    .tab
      display flex
      width: 100%
      height: 40px
      line-height: 40px
      /*border-bottom: 1px solid*/
      border-1px(rgba(7, 17, 27, 0.1))
      color: rgba(100, 100, 100, 0.9)
      .tab-item
        flex: 1
        text-align: center
        .r-link192
          display: block
          font-weight: 700
          font-size: 14px
          line-height: 40px
          color: rgb(77,200,93)
          &.active
            color: rgb(240, 200, 222)
</style>
