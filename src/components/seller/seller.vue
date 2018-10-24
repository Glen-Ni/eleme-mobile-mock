<template>
  <div class="seller" ref="seller">
    <div class="seller-content">
      <div class="overview">
        <h1 class="title">{{seller.name}}</h1>
        <div class="desc border-1px">
          <star :size="36" :score="seller.score"></star>
          <span class="text">({{seller.ratingCount}})</span>
          <span class="text">月售{{seller.sellCount}}单</span>
        </div>
        <ul class="remark">
          <li class="block borderR-1px">
            <h2>起送价</h2>
            <div class="content">
              <span class="stress">{{seller.minPrice}}</span>元
            </div>
          </li>
          <li class="block borderR-1px">
            <h2>商家配送</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryPrice}}</span>元
            </div>
          </li>
          <li class="block">
            <h2>平均配送时间</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryTime}}</span>分钟
            </div>
          </li>
        </ul>
      </div>
      <split></split>
      <div class="bulletin">
        <h1 class="tittle">公告与活动</h1>
        <p class="text border-1px">{{seller.bulletin}}</p>
        <ul>
          <li class="support border-1px" v-for="(support, index) in seller.supports" :key="index">
            <span class="icon" :class="classMap[support.type]"></span>
            <div class="description">{{support.description}}</div>
          </li>
        </ul>
      </div>
      <split></split>
      <split></split>
      <div class="infos">
        <h1 class="tittle border-1px">商家信息</h1>
        <ul>
          <li class="info border-1px" v-for="(info, index) in seller.infos" :key="index">{{info}}</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll'
  import star from '../star/star'
  import split from '../split/split'

  export default {
    name: 'seller',
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        classMap: ['decrease', 'discount', 'guarantee', 'invoice', 'special']
      }
    },
    mounted() {
      this.$nextTick(() => {
        this.scroll = new BScroll(this.$refs.seller)
      })
    },
    components: {
      star,
      split
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .seller
    position: absolute
    top: 174px
    bottom: 0
    left: 0
    width: 100%
    overflow: hidden
    .seller-content
      .overview
        padding: 18px
        .tittle
          line-height: 14px
          font-size: 14px
          color: rgb(7, 17, 27)
        .desc
          margin-top: 8px
          padding-bottom: 18px
          border-1px(rgba(7, 17, 27, 0.1))
          font-size: 0
          .star, .text
            display: inline-block
            vertical-align: top
          .star
            margin-right: 8px
          .text
            margin-right: 12px
            line-height: 18px // 给父元素设置lh的话整个会被撑高，原因不明
            font-size: 10px
            color: rgb(77, 85, 93)
        .remark
          margin-top: 18px // 这里用flex布局也行
          text-align: center
          height: 38px
          .block
            float: left
            font-size: 0
            width: 33%
            borderR-1px(rgba(7, 17, 27, 0.1))
            &:last-child
              border-none()
            h2
              line-height: 10px
              font-size: 10px
              color: rgb(147, 153, 159)
            .content
              margin-top: 4px
              font-size: 10px
              color: rgb(7, 17, 27)
              .stress
                line-height: 24px
                font-size: 24px
                font-weight: 200
      .bulletin
        padding: 18px 18px 0px 18px
        .tittle
          line-height: 14px
          font-size: 14px
          color: rgb(7, 17, 27)
        .text
          padding: 8px 12px 16px 12px
          line-height: 24px
          font-size: 12px
          font-weight: 200
          color: rgb(240, 20, 20)
          border-1px(rgba(7, 17, 27, 0.1))
        .support
          padding: 16px 12px
          border-1px(rgba(7, 17, 27, 0.1))
          font-size: 0
          &:last-child
            border-none()
          .icon, .description
            display: inline-block
            vertical-align: top
          .icon
            width: 16px
            height: 16px
            margin-right: 6px
            background-size: 16px 16px
            background-repeat: no-repeat
            &.decrease
              bg-image('decrease_4')
            &.discount
              bg-image('discount_4')
            &.guarantee
              bg-image('guarantee_4')
            &.invoice
              bg-image('invoice_4')
            &.special
              bg-image('special_4')
          .description
            line-height: 16px
            font-size: 12px
            font-weight: 200
            color: rgb(7, 17, 27)
      .infos
        padding: 18px 18px 0
        .tittle
          padding-bottom: 12px
          line-height: 14px
          font-size: 14px
          color: rgb(7, 17, 27)
          border-1px(rgba(7, 17, 27, 0.1))
        .info
          padding:18px 12px
          line-height: 16px
          font-size: 12px
          font-weight: 200
          color: rgb(7, 17, 27)
          border-1px(rgba(7, 17, 27, 0.1))
          &:last-child
            border-none()
</style>
