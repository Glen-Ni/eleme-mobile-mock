<template>
  <div>
    <div class="shopCart">
      <div class="content">
        <div class="content-left" @click="toggleList">
          <div class="logo-wrapper">
            <div class="logo" :class="{'highlight':totalCount>0}">
              <i class="icon-shopping_cart" :class="{'highlight':totalCount>0}"></i>
            </div>
            <div class="num" v-show="totalCount>0">{{totalCount}}</div>
          </div>
          <div class="price" :class="{'highlight':totalCount>0}">￥{{totalPrice}}</div>
          <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
        </div>
        <div class="content-right" @click="pay">
          <div class="delivery-price" :class="{'highlight':this.totalPrice >= this.minPrice}">{{payDesc}}</div>
        </div>
      </div>
      <div class="ball-container" v-for="(ball,index) in balls" v-bind:key="index">
        <transition
            v-on:before-enter="beforeEnter"
            v-on:enter="enter"
            v-on:after-enter="afterEnter"
            name="drop">
          <div class="ball" v-show="ball.show">
            <div class="inner inner-hook">
            </div>
          </div>
        </transition>
      </div>
      <transition name="fold">
        <div class="shopCart-list" v-show="listShow">
          <div class="list-header">
            <h1 class="title">购物车</h1>
            <span class="empty" @click="empty">清空</span>
          </div>
          <div class="list-content" ref="listContent">
            <ul>
              <li class="food border-1px" v-for="(food, index) in selectFoods" v-bind:key="index">
                <span class="name">{{food.name}}</span>
                <span class="price">￥{{food.price * food.count}}</span>
                <div class="shopcart-wrapper">
                  <cartcontrol :food="food" v-on:add="drop"></cartcontrol>
                </div>
              </li>
            </ul>
          </div>
        </div>
      </transition>
    </div>
    <transition name="fade">
      <div class="list-mask" v-show="listShow" @click="listHide"></div>
    </transition>
  </div>
</template>

<script type="text/ecmascript-6">
  import cartcontrol from '../cartcon/cartcontrol'
  import BScroll from 'better-scroll'

  export default {
    name: 'shopcart',
    props: {
      selectFoods: {
        type: Array
      },
      deliveryPrice: {
        type: Number,
        default: 0
      },
      minPrice: {
        type: Number,
        default: 0
      }
    },
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
    computed: {
      totalPrice() {
        let total = 0;
        this.selectFoods.forEach((food) => {
          total += food.price * food.count
        });
        return total
      },
      totalCount() {
        let count = 0;
        this.selectFoods.forEach((food) => {
          count += food.count
        });
        return count
      },
      totalKinds() {
        let kinds = 0;
        this.selectFoods.forEach(() => {
          kinds++
        });
        return kinds
      },
      payDesc() {
        if (this.totalPrice === 0) {
          return `￥${this.minPrice}起送`
        } else if (this.totalPrice < this.minPrice) {
          return `还差￥${this.minPrice - this.totalPrice}起送`
        } else {
          return '去结算'
        }
      },
      listShow() {
        if (!this.totalCount) {
          this.toggleList(); // 把fold设置为true(computed里面不能直接设置），防止二次添加自动弹出
          return false;
        }
        if (this.totalCount > 0 && !this.fold) {
          this.$nextTick(() => {
            this._initScroll()
          });
          // this.scroll.refresh()
          // this._initScroll();
          // console.log('listshow');
          return true;
        }
        return false;
      }
    },
    // created() {
    //   this.$nextTick(() => {
    //     if (!this.scroll) {
    //       this.scroll = new BScroll(this.$refs.listContent, {
    //         click: true
    //       });
    //     } else {
    //       this.scroll.refresh();
    //     }
    //   })
    // },
    methods: {
      drop(el) {
        for (let i = 0; i < this.balls.length; i++) {
          let ball = this.balls[i];
          if (!ball.show) {
            ball.show = true;
            ball.el = el;
            this.dropBalls.push(ball);
            console.log(this.dropBalls);
            return
          }
        }
      },
      beforeEnter(ele) {
        let count = this.balls.length;
        while (count--) {
          let ball = this.balls[count];
          if (ball.show) {
            let rect = ball.el.getBoundingClientRect();
            let x = rect.left - 32;
            let y = -(window.innerHeight - rect.top - 22);
            ele.style.display = '';
            ele.style.webkitTransform = `translate3d(0,${y}px,0)`;
            ele.style.transform = `translate3d(0,${y}px,0)`;
            let inner = ele.getElementsByClassName('inner-hook')[0];
            inner.style.webkitTransform = `translate3d(${x}px,0,0)`;
            inner.style.transform = `translate3d(${x}px,0,0)`
          }
        }
      },
      enter(el, done) {
        // console.log(el)
        /* eslint-disable no-unused-vars */
        let rf = el.offsetHeight;// 触发重绘
        this.$nextTick(() => {
          el.style.webkitTransform = 'translate3d(0,0,0)';
          el.style.transform = 'translate3d(0,0,0)';
          let inner = el.getElementsByClassName('inner-hook')[0];
          inner.style.webkitTransform = 'translate3d(0,0,0)';
          inner.style.transform = 'translate3d(0,0,0)';
          el.addEventListener('transitionend', done);
        })
      },
      afterEnter(el) {
        // console.log(el)
        let ball = this.dropBalls.shift();
        if (ball) {
          ball.show = false;
          el.style.display = 'none'
        }
      },
      toggleList() {
        if (!this.totalCount || this.totalCount === 0) {
          this.fold = true
          return
        }
        this.fold = !this.fold
      },
      _initScroll() {
        if (!this.scroll) {
          // setTimeout(() => {
          //   this.scroll = new BScroll(this.$refs.listContent, {
          //     click: true
          //   })
          // }, 50)
          this.scroll = new BScroll(this.$refs.listContent, {
            click: true
          });
          this.scroll.scrollTo(0, 1, 10)
        } else {
          this.scroll.refresh();
        }
      },
      empty() {
        this.selectFoods.forEach((food) => {
          food.count = 0;
        })
      },
      listHide() {
        this.fold = true
      },
      pay() {
        if (this.totalPrice < this.minPrice) {
          return
        }
        window.alert(`支付${this.totalPrice}元`)
      }
    },
    components: {
      cartcontrol
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  @import "../../common/stylus/base.styl"
  .shopCart
    position: fixed
    left: 0
    bottom: 0
    z-index: 50
    width: 100%
    height: 48px
    .content
      display: flex
      background-color: #141d27
      font-size: 0 // 防止间隙
      .content-left
        flex: 1
        .logo-wrapper
          display: inline-block
          position: relative
          top: -10px
          margin: 0 12px
          padding: 6px
          width: 56px
          height: 56px
          box-sizing: border-box
          vertial-align: top
          border-radius: 50%
          background-color: #141d27
          .logo
            width: 100%
            height: 100%
            border-radius: 50%
            background-color: #2b343c
            text-align center
            &.highlight
              background-color: rgb(0, 160, 220)
            .icon-shopping_cart
              line-height: 44px
              font-size: 24px
              color: #80858a
              &.highlight
                color: #fff
          .num
            position: absolute
            top: 0
            right: 0
            width: 24px
            height: 16px
            line-height: 16px
            border-radius: 8px
            background-color: rgb(240, 20, 20)
            box-shadow: 0 4px 8px px rgba(0, 0, 0, 0.4)
            font-size 9px
            font-weight: 700
            color: #fff
            text-align: center
        .price
          display: inline-block
          vertical-align: top
          margin: 12px 0
          padding-right: 12px
          line-height: 24px
          font-size: 16px
          color: rgba(255, 255, 255, 0.4)
          font-weight: 700
          border-right: 1px solid rgba(255, 255, 255, 0.1)
          &.highlight
            color: #fff
        .desc
          display: inline-block
          vertical-align: top
          margin: 0 12px
          font-size: 12px
          color: rgba(255, 255, 255, 0.4)
          line-height: 48px
      .content-right
        flex: 0 0 105px
        width: 105px
        background-color: rgba(255, 255, 255, 0.1)
        text-align: center
        .delivery-price
          padding: 0 8px
          height: 48px
          line-height: 48px
          font-size: 12px
          font-weight: 700
          text-align: content
          color: rgba(255, 255, 255, 0.4)
          &.highlight
            background-color: #00c43c
            color: #fff
    .ball-container
      .ball
        position: fixed
        left: 32px
        bottom: 32px
        z-index: 200
        transition: all 0.4s cubic-bezier(0.49, -0.29, 0.75, 0.41)
        .inner
          width: 16px
          height: 16px
          border-radius: 50%
          background-color: rgb(0, 160, 220)
          transition: all 0.4s linear
    .shopCart-list
      position: absolute
      left: 0
      top: 0
      z-index: -2
      width: 100%
      transform: translate3d(0, -100%, 0)
      background-color: #fff
      &.fold-enter-active, &.fold-leave-active
        transition: all 0.5s;
      &.fold-enter, &.fold-leave-to
        transform: translate3d(0, 0, 0)
      .list-header
        position: relative
        height: 40px
        line-height: 40px
        padding: 0 18px
        background: #f3f5f7
        border-bottom: 1px solid rgba(7, 17, 27, 0.1)
        &.special
          bottom: 40px
        .title
          float: left
          font-size: 14px
          color: rgb(7, 17, 27)
        .empty
          float: right
          font-size: 12px
          color: rgb(0, 160, 220)
      .list-content
        padding: 0 18px
        max-height: 177px
        background: #fff
        overflow: hidden
        z-index: -1
        .food
          position relative
          padding: 12px 0
          box-sizing border-box
          border-1px(rgba(7, 17, 27, 0.1))
          height: 48px
          .name
            line-height: 24px
            font-size: 14px
            color: rgb(7, 17, 27)
          .price
            position: absolute
            right: 90px
            border-top: 12px
            font-weight: 700
            line-height: 24px
            font-size: 14px
            color: rgb(240, 20, 20)
          .shopcart-wrapper
            position: absolute
            right: 0
            bottom: 6px

  .list-mask
    position: fixed
    top: 0
    left: 0
    width: 100%
    height: 100%
    z-index: 40
    background: rgba(7, 17, 27, 0.6)
    filter: blur(10px)
    &.fade-enter-active, &.fade-leave-active
      transition: all 0.5s
    &.fade-enter, &.fade-leave-to
      opacity: 0
      background: rgba(7, 17, 27, 0)
</style>
