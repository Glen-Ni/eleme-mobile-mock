<template>
  <div class="header">
    <div class="content-wrapper">
      <div class="avatar">
        <img width="64" height="64" :src="seller.avatar" >
      </div>
      <div class="content">
        <div class="title">
          <span class="brand"></span>
          <span class="name">{{seller.name}}</span>
        </div>
        <div class="description">
          {{seller.description}}/{{seller.deliveryTime}}分钟送达
        </div>
        <div v-if="seller.supports" class="support">
          <span class="icon" :class="classMap[seller.supports[0].type]"></span>
          <div class="text">{{seller.supports[0].description}}</div>
        </div>
      </div>
      <div v-if="seller.supports" class="support-count" @click="showDetail">
        <span class="count">{{seller.supports.length}}个</span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <div class="bulletin-wrapper">
      <span class="bulletin-title"></span><span class="bulletin-text">{{seller.bulletin}}</span>
      <i class="icon-keyboard_arrow_right"></i>
    </div>
    <div class="background">
      <img :src="seller.avatar" width="100%" height="100%">
    </div>
    <transition><div v-show="detailShow" class="detail">
      <div class="detail-wrapper clearfix">
        <div class="detail-main">
          <h1 class="name">{{seller.name}}</h1>
          <div class="star-wrapper">
            <star :size="48" :score="seller.score"></star>
          </div>
          <div class="title">
            <div class="line"></div>
            <div class="text">优惠信息</div>
            <div class="line"></div>
          </div>
          <ul v-if="seller.supports" class="supports">
            <li v-for="(support,index) in seller.supports" :key="index" class="support">
              <span class="icon" :class="classMap[support.type]"></span>
              <div class="text">{{support.description}}</div>
            </li>
          </ul>
          <div class="title">
            <div class="line"></div>
            <div class="text">商家公告</div>
            <div class="line"></div>
          </div>
          <div class="bulletin-text">
            <p class="content">{{seller.bulletin}}</p>
          </div>
        </div>
      </div>
      <div class="detail-close" @click="hideDetail">
        <i class="icon-close"></i>
      </div>
    </div></transition>
  </div>
</template>

<script type="text/ecmascript-6">
  import star from '../star/star.vue'
  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data () {
      return {
        detailShow: false
      }
    },
    methods: {
      showDetail: function () {
        this.detailShow = true
      },
      hideDetail: function () {
        this.detailShow = false
      }
    },
    created () {
      this.classMap = ['decrease', 'discount', 'guarantee', 'invoice', 'special']
    },
    components: {
      star
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin"
  .header
    position:relative
    color: #ffffff
    background:rgba(7,17,27,0.5)
    overflow: hidden
    .content-wrapper
      position: relative
      padding: 24px 12px 18px 24px
      font-size: 0
      .avatar
        display: inline-block
        img
          border-radius: 2px
      .content
        display: inline-block
        /*font-size: 14px*/
        margin-left: 16px
        vertical-align: top
        .title
          margin: 2px 0 8px 0
          .brand
            display: inline-block
            vertical-align top
            width: 30px
            height: 18px
            bg-image('brand')
            background-size: 30px 18px
            background-repeat: no-repeat
          .name
            display: inline-block
            margin-left: 6px
            /*vertical-align top*/
            font-size: 16px
            line-height: 18px
            font-weight: bold
        .description
          margin-bottom: 10px
          line-height: 12px
          font-size:12px
        .support
          .icon
            display: inline-block
            vertical-align top
            width: 12px
            height: 12px
            margin-right: 4px
            background-size: 12px 12px
            background-repeat: no-repeat
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
            display: inline-block
            vertical-align top
            font-size:10px
            line-height: 12px
      .support-count
        position:absolute
        right: 12px
        bottom: 14px
        padding: 0 8px
        height: 24px
        line-height: 24px
        border-radius: 14px
        background-color: rgba(0,0,0,0.2)
        text-align center
        .count
          vertial-align: middle
          line-height: 24px
          font-size: 10px
        .icon-keyboard_arrow_right
          text-align center
          vertial-align: middle
          margin-left:2px
          line-height: 24px
          font-size:10px
    .bulletin-wrapper
      position: relative
      height: 28px
      line-height 28px
      background: rgba(7,17,27,0.2)
      padding:0 22px 0 12px
      white-space: nowrap
      overflow: hidden
      text-overflow: ellipsis
      .bulletin-title
        vertical-align top
        display: inline-block
        margin-top: 8px
        width: 22px
        height: 12px
        bg-image('bulletin')
        background-size: 22px 12px
        background-repeat: no-repeat //no-repeat使得在同一行
      .bulletin-text
        vertical-align top
        margin: 0 4px
        font-size:10px
      .icon-keyboard_arrow_right
        position: absolute
        font-size: 10px
        right: 12px
        bottom: 8px
    .background
      position: absolute
      top:0
      left:0
      width: 100%
      height: width
      z-index: -1
      filter:blur(10px) //背景模糊
    .v-enter-active, .v-leave-active
      transition: all 0.5s
    .v-enter, .v-leave-to
      opacity: 0
      background: rgba(7, 17, 27, 0)
    .detail
      position: fixed
      z-index: 100
      top: 0
      left: 0
      width: 100%
      height: 100%
      overflow: auto //超出滚动
      background: rgba(7, 17, 27, 0.8)
      .detail-wrapper
        width: 100%
        min-height: 100% //最小高度 自适应
        .detail-main
          margin-top: 64px
          padding-bottom: 64px
          .name
            font-size 16px
            color: rgb(255, 255, 255)
            line-height: 16px
            text-align: center
          .star-wrapper
            margin-top: 18px
            padding: 2px 0
            text-align: center
          .title
            display: flex
            width:80%
            margin: 28px auto 24px
            .text
              padding: 0 12px
              font-size: 16px
              font-weight: 700
              color: rgb(255, 255, 255)
              text-align center
              line-height: 14px
            .line
              flex: 1
              position: relative
              top: -7px
              border-bottom: 1px solid rgba(255,255,255,0.2)
          .supports
            width:80%
            margin: 0 auto
            .support
              height: 16px
              margin-bottom:12px
              margin-left: 12px
              &:last-child
                margin-bottom: 0
              .icon
                display: inline-block
                vertical-align top
                width: 16px
                height: 16px
                margin-right: 6px
                background-size: 16px 16px
                background-repeat: no-repeat
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
                display: inline-block
                vertical-align top
                font-size: 12px
                font-weight: 200
                color: rgb(255, 255, 255)
                line-height: 16px
          .bulletin-text
            width: 80%
            margin: 0 auto
            .content
              padding: 0 12px
              font-size 12px
              font-weight: 200
              color: rgb(255, 255, 255)
              line-height: 24px
      .detail-close
        position: relative
        width: 32px
        height: 32px
        margin: -64px auto 0
        clear: both
        font-size: 32px
</style>
