<template>
  <div class="cartcontrol">
    <transition>
      <div class="cart-decrease" v-show="food.count>0" @click.stop.prevent="decreaseCart">
        <span class="inner icon-remove_circle_outline"></span>
      </div>
    </transition>
    <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
    <div class="cart-add icon-add_circle" @click.stop.prevent="addCart"></div>
  </div>
</template>

<script type="text/ecmascript-6">
  import Vue from 'vue'

  export default {
    name: 'cartcontrol',
    props: {
      food: {
        type: Object
      }
    },
    methods: {
      addCart: function (event) {
        if (!event._constructed) { // 触发两次的bug，不过现在貌似没有了
          return
        }
        if (!this.food.count) {
          Vue.set(this.food, 'count', 1)
        } else {
          this.food.count++
        }
        this.$emit('add', event.target)
      },
      decreaseCart: function () {
        if (!event._constructed) { // 触发两次的bug，不过现在貌似没有了
          return
        }
        if (this.food.count > 0) {
          this.food.count--
        }
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .cartcontrol
    font-size 0
    position: relative
    .cart-decrease
      position: absolute
      bottom: 0
      right: 48px
      display: inline-block
      padding: 6px
      .inner
        display: inline-block
        line-height: 24px
        font-size: 24px
        color: rgb(0, 160, 220)
        transform: rotate(0deg)
      &.v-enter-active, &.v-leave-active
        transition: all .4s ease
        .inner
          transition: all .4s ease
      &.v-enter, &.v-leave-to
        opacity: 0
        transform: translateX(24px)
        .inner
          transform: rotate(180deg)
    .cart-count
      display: inline-block
      vertical-align: top
      width: 12px
      padding-top: 6px
      line-height: 24px
      font-size: 10px
      color: rgb(147, 153, 159)
      text-align: center
    .cart-add
      display: inline-block
      line-height: 24px
      font-size: 24px
      padding: 6px
      color: rgb(0, 160, 220)
</style>
